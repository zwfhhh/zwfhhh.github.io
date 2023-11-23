---
layout: post
title: 混合推荐算法在spring boot vue项目中的应用
date: 2023-11-23 23:41:00 +0800
category: algorithm
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/project.jpg
icon: note
---


* content
{:toc}
# 混合推荐算法（在用户对文物的评分情景下）

## 为什么需要使用推荐算法

基于用户行为以及人物内容的方式来给系统增加推荐内容，使得平台的内容更加人性化，增加用户使用平台的体验

## 基于内容的推荐算法

具体推荐算法的含义[1],代码

```java
package com.springboot.recommend;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.ListIterator;
import java.util.Map;

public class FormatUtil {
	
	/**
	 * 去除停用词
	 * @param content 原中文文本
	 * @return 去除后的文本
	 * @throws Exception
	 */
	@SuppressWarnings("all")
	public static List<String> RemovalOfStopWords(List<String> content) throws Exception{
		
		InputStream inputStream = FormatUtil.class.getClassLoader().getResourceAsStream("file/baidu_stopwords.txt");
		BufferedReader br = new BufferedReader(new InputStreamReader(inputStream));
		Map<String,String> map = new HashMap<>();
		String value;
		while ((value = br.readLine()) != null) {
			map.put(value, null);
		}
		ListIterator<String> iterator = content.listIterator();
		//iterator迭代器，使用hasNext()检查序列中是否还有元素。
		while (iterator.hasNext()) {
			String next = iterator.next();
			if(map.containsKey(next)){
				iterator.remove();				
			}
		}

		return content;
	}	

}

----------------------------------------------------
package com.springboot.recommend;

import static java.util.Map.Entry.comparingByValue;
import static java.util.stream.Collectors.toMap;

import java.util.Collections;
import java.util.HashMap;
import java.util.Iterator;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;

import com.springboot.sys.entity.RelicInformation;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

@SuppressWarnings("all")
public class OfflineRecommend {
	/**
	 * 基于TF-IDF的文物推荐 混合推荐改进版本
	 * @param total 文物总数量
	 * @param newsList 文物列表
	 * @return 文物ID的推荐列表，列表使用,符号分隔
	 */
	public static Map<String,Double> recommend(int total,List<RelicInformation> newsList,Integer itemId){
		// 文档分词后的map，存放getContentSplitList方法后的每篇文章的词语
		Map<String,List<String>> wordList = new HashMap<>();
		// 得到每个文物分词后的结果，存放在wordList中，采用jieba分词
		for (RelicInformation relicInformation : newsList) {
			wordList.put(relicInformation.getId()+"", RecommendUtil.getContentSplitList(relicInformation.getRelicIntroduce()));
		}

		// 存放关键词列表
		Map<String,String> sim = new HashMap<>();

		// 外层循环控制次数，循环分词后的列表
		for (Entry<String,List<String>> content : wordList.entrySet()) {
			//每条entry就是一个文物id +它对应的关键词列表
			// tf计算，每个词语+对应词频
			Map<String, Float> tfCalculate = RecommendUtil.tf(content.getValue());
//			System.out.println("词频统计："+tfCalculate);
			// tfidf值计算
			Map<String, Float> tfidfCalculate = RecommendUtil.tfidf(total, wordList, tfCalculate);
//			System.out.println(tfidfCalculate);
			// 用来存放推荐的文物ID
			StringBuilder sb = new StringBuilder();
			// 遍历计算出来的map
			//iterator迭代器，使用hasNext()检查序列中是否还有元素。
			Iterator<String> iterator = tfidfCalculate.keySet().iterator();
			while (iterator.hasNext()) {
				sb.append(iterator.next());
				if(iterator.hasNext()){
					sb.append(",");
				}
			}
			// 存入关键词集合中，sim中为文章id+[关键词字符串（以,分割）]
			sim.put(content.getKey(), sb.toString());
		}
		//System.out.println(sim);

		// 存放推荐列表
//		Map<String,String> recommend = new HashMap<>();

		// 两两计算相似矩阵，这里的entry是文章id+[关键词字符串（以,分割）]
		for (Entry<String, String> simCon : sim.entrySet()) {

			if(!simCon.getKey().equals(itemId.toString())){
					continue;
			}
			Map<String,Double> recTop = new HashMap<>();
			for (Entry<String, String> simConIn : sim.entrySet()) {
				// 排除自己和自己做余弦相似度
				if(simCon.getKey()!=simConIn.getKey()){
					// 计算两个词频的相似度，这里的value放置的是每篇文章的关键词列表字符串
					double similarDegree = RecommendUtil.getSimilarDegree(simCon.getValue(),simConIn.getValue());
					recTop.put(simConIn.getKey(), similarDegree);
				}
			}
			return recTop;
			// 排序：选择相似度最高的5个文物
			// 按值排序降序
		}
		return null;
	}

}

-------------------------------------------------
package com.springboot.recommend;

import static java.util.Map.Entry.comparingByValue;
import static java.util.stream.Collectors.toMap;

import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.Iterator;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import com.huaban.analysis.jieba.JiebaSegmenter;

@SuppressWarnings("all")
public class RecommendUtil {

	// 将文物内容进行分词成数组列表
	public static List<String> getContentSplitList(String content) {
		String delHTMLTag = delHTMLTag(content);
//		// 替换语气词
		String sentences = delHTMLTag.trim().replaceAll(" ", "");
		// Jieba分词器
		List<String> list = new JiebaSegmenter().sentenceProcess(sentences);
		List<String> removalOfStopWords = null;
		try {
			// 删除语气词
			removalOfStopWords = FormatUtil.RemovalOfStopWords(list);
		} catch (Exception e) {
			e.printStackTrace();
		}
//		System.out.println("删除后的语气词："+removalOfStopWords);
		return removalOfStopWords;
	}

	/**
	 * 计算每个文档的tf值
	 * @param wordList 文物内容划分的词
	 * @return 单词在文章中出现的频率map
	 */
	public static Map<String, Float> tf(List<String> wordList) {

		// 计算每个单词次数
		Map<String, Integer> amountWord = new HashMap<String, Integer>();
		//for循环遍历存放了文章分词后词语的wordList
		for (String string : wordList) {
			//判断amountWord里面是否有那个key，如果没有就赋初始值为1，有的话就加1
			if (!amountWord.containsKey(string)) {
				amountWord.put(string, 1);
			} else {
				amountWord.put(string, amountWord.get(string).intValue() + 1);
			}
		}
		// 删除语气词
		amountWord.remove("，");
		amountWord.remove(",");
		amountWord.remove("。");
		amountWord.remove("的");
		amountWord.remove("是");
		amountWord.remove("和");
		amountWord.remove("了");
		amountWord.remove("“");
		amountWord.remove("”");

//		System.out.println("单词出现的次数：" + amountWord);

		// 计算词频TF
		//将每个词语的次数除以文章的总次数得到每个词语的词频
		Map<String, Float> tfWord = new HashMap<String, Float>();
		for (Entry<String, Integer> string : amountWord.entrySet()) {
			tfWord.put(string.getKey(), Float.valueOf(string.getValue()) / wordList.size());
		}
//		System.out.println("单词在文章中出现的频率" + tfWord);

		// 按值排序降序
		Map<String, Float> sorted = tfWord.entrySet().stream().sorted(Collections.reverseOrder(comparingByValue()))
				.collect(toMap(Entry::getKey, Entry::getValue, (e1, e2) -> e2, LinkedHashMap::new));

//		System.out.println("降序按值排序后的map: " + sorted);

		return sorted;
	}
	
	/**
	 * 计算每个文物的tfidf词
	 * @param D 总文档数
	 * @param doc_words 每个文档对应的分词
	 * @param tf 计算好的tf,用这个作为基础计算tfidf
	 * @return 每个文档中的单词的tfidf的值
	 */
    public static Map<String,Float> tfidf(int D, Map<String,List<String>> doc_words,Map<String,Float> tf){

        HashMap<String,Float> tfidf=new HashMap<String, Float>();
        for(String key:tf.keySet()){
        	//包含某个词语的文档的数量
            int Dt=0;
            //遍历迭代，for循环每个文档对应的分词，entrySet()该方法遍历map，返回值就是这个map中各个键值对映射关系的集合。
            for(Entry<String, List<String>> entry:doc_words.entrySet()){
            	List<String> value = entry.getValue();
            	//words是存放值的大小的数组
            	String[] words = new String[value.size()];
            	value.toArray(words);
                List<String> wordlist=new ArrayList<String>();
                for(int i=0;i<words.length;i++){
                    wordlist.add(words[i]);
                }
                if(wordlist.contains(key)){
                    Dt++;
                }
            }
            //计算IDF值
            float idfvalue=(float) Math.log(Float.valueOf(D)/Dt);
            tfidf.put(key, idfvalue * tf.get(key));
        }       
        // 按值排序降序
        Map<String, Float> sorted = tfidf
                .entrySet()
                .stream()
                .sorted(Collections.reverseOrder(comparingByValue()))
                .collect(
                        toMap(Entry::getKey, Entry::getValue, (e1, e2) -> e2,
                                LinkedHashMap::new));
        
        // 选取前10个关键字

        
        return sorted;
    }
    
    /**
     * 计算两个字符串(英文字符)的相似度，简单的余弦计算，未添权重
     * @param str1 第一个文物关键字内容，每个关键字用，分割
     * @param str2 第二个文物关键字内容，每个关键字用，分割
     * @return 返回相似度值
     */
	public static double getSimilarDegree(String str1, String str2) {
		// 创建向量空间模型，使用map实现
		//数组[0]中存放的是文物1的词频，数组[1]中存放的是文物2的词频，key是关键词
		Map<String, int[]> vectorSpace = new HashMap<String, int[]>();
		int[] itemCountArray = null;// 为了避免频繁产生局部变量，所以将itemCountArray声明在此
		// 以，为分隔符，分解字符串
		//for循环计算得到两条文物的关键词词频向量
		String strArray[] = str1.split(",");
		for (int i = 0; i < strArray.length; ++i) {
			if (vectorSpace.containsKey(strArray[i]))
				++(vectorSpace.get(strArray[i])[0]);
			else {
				//初始化数据
				itemCountArray = new int[2];
				//数组[0]中存放的是文物1的词频，数组[1]中存放的是文物2的词频，key是关键词
				itemCountArray[0] = 1;
				itemCountArray[1] = 0;
				vectorSpace.put(strArray[i], itemCountArray);
			}
		}
		strArray = str2.split(",");
		for (int i = 0; i < strArray.length; ++i) {
			if (vectorSpace.containsKey(strArray[i]))
				++(vectorSpace.get(strArray[i])[1]);
			else {
				//数组[0]中存放的是文物1的词频，数组[1]中存放的是文物2的词频
				//数据初始化
				itemCountArray = new int[2];
				itemCountArray[0] = 0;
				itemCountArray[1] = 1;
				vectorSpace.put(strArray[i], itemCountArray);
			}
		}
		// 计算相似度
		double vector1Modulo = 0.00;// 向量1的模 文物1
		double vector2Modulo = 0.00;// 向量2的模 文物2
		double vectorProduct = 0.00; // 向量积  分子
		Iterator iter = vectorSpace.entrySet().iterator();

		while (iter.hasNext()) {
			//这里的entry是 key:关键词 + value [0]文物1中的词频，[1]文物2中的词频
			Entry entry = (Entry) iter.next();
			itemCountArray = (int[]) entry.getValue();
			vector1Modulo += itemCountArray[0] * itemCountArray[0];
			vector2Modulo += itemCountArray[1] * itemCountArray[1];
			vectorProduct += itemCountArray[0] * itemCountArray[1];
		}
		vector1Modulo = Math.sqrt(vector1Modulo);  //分母1
		vector2Modulo = Math.sqrt(vector2Modulo);  //分母2
		// 返回相似度
		return (vectorProduct / (vector1Modulo * vector2Modulo));
	}
	
	/**
     * 去除字符串中HTML标签
     * @param htmlStr HTML字符串
     * @return 返回删除HTML标签的字符串
     */
    private static String delHTMLTag(String htmlStr) {
        if (htmlStr == null || "".equals(htmlStr)) {
            return "";
        }
        String regEx_script = "<script[^>]*?>[\\s\\S]*?<\\/script>"; // 定义script的正则表达式
        String regEx_style = "<style[^>]*?>[\\s\\S]*?<\\/style>"; // 定义style的正则表达式
        String regEx_html = "<[^>]+>"; // 定义HTML标签的正则表达式
        // 过滤script标签
        Pattern p_script = Pattern.compile(regEx_script, Pattern.CASE_INSENSITIVE);
        Matcher m_script = p_script.matcher(htmlStr);
        htmlStr = m_script.replaceAll("");
        // 过滤style标签
        Pattern p_style = Pattern.compile(regEx_style, Pattern.CASE_INSENSITIVE);
        Matcher m_style = p_style.matcher(htmlStr);
        htmlStr = m_style.replaceAll("");
        // 过滤html标签
        Pattern p_html = Pattern.compile(regEx_html, Pattern.CASE_INSENSITIVE);
        Matcher m_html = p_html.matcher(htmlStr);
        htmlStr = m_html.replaceAll("");
        return htmlStr;
    }
}

```

关于什么是TF-IDF[2]

## 基于用户或物品的协同过滤算法

具体推荐算法的含义[1],代码

```java
package com.springboot.sys.core;



import com.springboot.sys.entity.RelateDTO;
import org.assertj.core.util.Lists;

import java.util.*;
import java.util.stream.IntStream;

/**
 * 核心算法
 *
 * @author tarzan
 * @version 1.0
 * @date 2020/7/31$ 15:21$
 * @since JDK1.8
 */
public class CoreMath {



    /**
     * 计算相关系数并排序
     * @param key
     * @param map
     * @return Map<Integer,Double>
     */
    public static Map<Integer,Double> computeNeighbor(Integer key, Map<Integer,List<RelateDTO>>  map, int type) {
        Map<Integer,Double> distMap = new TreeMap<>();
        List<RelateDTO> userItems=map.get(key);
        map.forEach((k,v)->{
            //排除此用户
            if(!k.equals(key)){
                //关系系数
                double coefficient = relateDist(v,userItems,type);
                //关系距离
             //   double distance=Math.abs(coefficient);
                distMap.put(k,coefficient);
            }
        });
        return distMap;
    }


    /**
     * 计算两个序列间的相关系数
     *
     * @param xList
     * @param yList
     * @param type 类型0基于用户推荐 1基于物品推荐
     * @return double
     */
    private static double relateDist(List<RelateDTO> xList, List<RelateDTO> yList,int type) {
        if(xList==null||yList==null){
            return 0D;
        }
        List<Double> xs= Lists.newArrayList();
        List<Double> ys= Lists.newArrayList();
        xList.forEach(x->{
            yList.forEach(y->{
                if(type==0){
                    if(x.getItemId().equals(y.getItemId())){
                        xs.add(x.getIndex());
                        ys.add(y.getIndex());
                    }
                }else{
                    if(x.getUseId().equals(y.getUseId())){
                        xs.add(x.getIndex());
                        ys.add(y.getIndex());
                    }
                }
            });
        });
        return getRelate(xs,ys);
    }

    /**
     * 方法描述: 皮尔森（pearson）相关系数计算
     *
     * @param xs x集合
     * @param ys y集合
     * @Return {@link double}
     * @author tarzan
     * @date 2020年07月31日 17:03:20
     */
    public static double getRelate(List<Double> xs, List<Double> ys){
        int n=xs.size();
        //至少有两个元素
        if (n<2) {
            return 0D;
        }
        double Ex= xs.stream().mapToDouble(x->x).sum();
        double Ey=ys.stream().mapToDouble(y->y).sum();
        double Ex2=xs.stream().mapToDouble(x->Math.pow(x,2)).sum();
        double Ey2=ys.stream().mapToDouble(y->Math.pow(y,2)).sum();
        double Exy= IntStream.range(0,n).mapToDouble(i->xs.get(i)*ys.get(i)).sum();
        double numerator=Exy-Ex*Ey/n;
        double denominator=Math.sqrt((Ex2-Math.pow(Ex,2)/n)*(Ey2-Math.pow(Ey,2)/n));
        if (denominator==0) {
            return 0D;
        }
        return numerator/denominator;
    }

}
---------------------------------------------
    package com.springboot.sys.core;


import com.springboot.sys.entity.RelateDTO;

import java.util.*;
import java.util.stream.Collectors;

/**
 * 核心算法
 *
 * @author tarzan
 * @version 1.0
 * @date 2020/7/31$ 15:21$
 * @since JDK1.8
 */
public class ItemCF {

//    /**
//     *
//     * @param itemId
//     * @param list
//     * @return
//     */
//    public static List<Integer> recommend(Integer itemId, List<RelateDTO> list,Integer getNum) {
//        //预处理数据
//        list = calculateAverageRating(list);
//
//        //按物品分组
//        Map<Integer, List<RelateDTO>>  itemMap=list.stream().collect(Collectors.groupingBy(RelateDTO::getItemId));
//        //获取其他物品与当前物品的关系值
//        Map<Integer,Double>  itemDisMap = CoreMath.computeNeighbor(itemId, itemMap,1);
//        //获取关系最近物品
//        double maxValue=Collections.max(itemDisMap.values());
//        System.out.println("物品最大相关系数为"+maxValue);
//        return getTopKeys(itemDisMap,getNum);
//    }


    /**
     *适应混合推荐改进返回值
     * @param itemId
     * @param list
     * @return
     */
    public static Map<Integer,Double> recommend(Integer itemId, List<RelateDTO> list) {
        //预处理数据
        list = calculateAverageRating(list);

        //按物品分组
        Map<Integer, List<RelateDTO>>  itemMap=list.stream().collect(Collectors.groupingBy(RelateDTO::getItemId));
        //获取其他物品与当前物品的关系值
        Map<Integer,Double>  itemDisMap = CoreMath.computeNeighbor(itemId, itemMap,1);
        //获取关系最近物品

//        double maxValue=Collections.max(itemDisMap.values());
//        System.out.println("物品最大相关系数为"+maxValue);
        return itemDisMap;
    }


    /**
     * 获取关系最近的前几个
     * @param map
     * @param getNum
     * @return
     */
    public static List<Integer> getTopKeys(Map<Integer, Double> map,Integer getNum) {
        List<Map.Entry<Integer, Double>> list = new ArrayList<>(map.entrySet());
        list.sort((o1, o2) -> o2.getValue().compareTo(o1.getValue()));

        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < Math.min(getNum, list.size()); i++) {
            result.add(list.get(i).getKey());
        }
        return result;
    }

    /**
     * 解决数据稀疏性的问题，重点
     * @param inputList
     * @return
     */
    public static List<RelateDTO> calculateAverageRating(List<RelateDTO> inputList) {
        // 计算每个用户对每个物品的平均评分
        Map<Integer, Map<Integer, Double>> userItemRatings = new HashMap<>();
        for (RelateDTO dto : inputList) {
            userItemRatings.putIfAbsent(dto.getUseId(), new HashMap<>());
            userItemRatings.get(dto.getUseId()).put(dto.getItemId(), dto.getIndex());
        }

        // 计算每个用户的平均评分
        Map<Integer, Double> userAverageRatings = new HashMap<>();
        for (Map.Entry<Integer, Map<Integer, Double>> entry : userItemRatings.entrySet()) {
            int userId = entry.getKey();
            Map<Integer, Double> itemRatings = entry.getValue();
            double sum = 0;
            for (double rating : itemRatings.values()) {
                sum += rating;
            }
            double average = sum / itemRatings.size();
            userAverageRatings.put(userId, average);
        }

        // 将没有评分的用户用平均分插入到List<RelateDTO>中
        Set<Integer> allUsers = new HashSet<>();
        Set<Integer> allItems = new HashSet<>();
        for (RelateDTO dto : inputList) {
            allUsers.add(dto.getUseId());
            allItems.add(dto.getItemId());
        }

        List<RelateDTO> resultList = new ArrayList<>(inputList);
        for (int userId : allUsers) {
            for (int itemId : allItems) {
                if (!userItemRatings.containsKey(userId) || !userItemRatings.get(userId).containsKey(itemId)) {
                    double averageRating = userAverageRatings.getOrDefault(userId, 0D);
                    RelateDTO newDTO = new RelateDTO(userId, itemId, averageRating);
                    resultList.add(newDTO);
                }
            }
        }

        return resultList;
    }


}
-------------------------------------------------
    package com.springboot.sys.core;

import com.springboot.sys.entity.RelateDTO;

import java.util.*;
import java.util.stream.Collectors;

/**
 * 核心算法
 *
 * @author tarzan
 * @version 1.0
 * @date 2020/7/31$ 15:21$
 * @since JDK1.8
 */
public class UserCF {

    /**
     *
     * @param userId
     * @param list
     * @return
     */
    public static List<Integer> recommend(Integer userId, List<RelateDTO> list) {

        //预处理数据
        List<RelateDTO> list1 = ItemCF.calculateAverageRating(list);
        //按用户分组
        Map<Integer, List<RelateDTO>>  userMap=list1.stream().collect(Collectors.groupingBy(RelateDTO::getUseId));

        Map<Integer, List<RelateDTO>>  userMap2=list.stream().collect(Collectors.groupingBy(RelateDTO::getUseId));
        //获取其他用户与当前用户的关系值
        Map<Integer,Double>  userDisMap = CoreMath.computeNeighbor(userId, userMap,0);
        //获取关系最近的用户
        double maxValue=Collections.max(userDisMap.values());
        Set<Integer> userIds=userDisMap.entrySet().stream().filter(e->e.getValue()==maxValue).map(Map.Entry::getKey).collect(Collectors.toSet());
        //取关系最近的用户
        Integer nearestUserId = userIds.stream().findAny().orElse(null);
        if(nearestUserId==null){
            return Collections.emptyList();
        }
        //最近邻用户看过物品列表
        List<Integer>  neighborItems = userMap2.get(nearestUserId).stream().map(RelateDTO::getItemId).collect(Collectors.toList());
        //指定用户看过物品列表
        List<Integer>  userItems  = userMap2.get(userId).stream().map(RelateDTO::getItemId).collect(Collectors.toList());
        //找到最近邻评分过，但是该用户没评分过
        neighborItems.removeAll(userItems);
        return neighborItems;
    }

}

```

上面代码具体参考文章[3]

## 仅仅使用协同过滤面临的问题

主要是系统的数据稀疏性问题和系统的新用户和新物品的冷启动问题,至于什么是稀疏性和冷启动问题[4]

### 解决数据的稀疏性问题

这个系统我们主要采用计算每个用户对已经评分物品的均分填充到未评分中，从而打到填充矩阵的效果，解决计算出来的Person相关系数为0的问题（这个是由于时间的问题，如果有需要大家可以试一试SVD[5]或者正态校正 的方式来预处理数据）,具体代码

```
/**
     * 解决数据稀疏性的问题，重点
     * @param inputList
     * @return
     */
    public static List<RelateDTO> calculateAverageRating(List<RelateDTO> inputList) {
        // 计算每个用户对每个物品的平均评分
        Map<Integer, Map<Integer, Double>> userItemRatings = new HashMap<>();
        for (RelateDTO dto : inputList) {
            userItemRatings.putIfAbsent(dto.getUseId(), new HashMap<>());
            userItemRatings.get(dto.getUseId()).put(dto.getItemId(), dto.getIndex());
        }

        // 计算每个用户的平均评分
        Map<Integer, Double> userAverageRatings = new HashMap<>();
        for (Map.Entry<Integer, Map<Integer, Double>> entry : userItemRatings.entrySet()) {
            int userId = entry.getKey();
            Map<Integer, Double> itemRatings = entry.getValue();
            double sum = 0;
            for (double rating : itemRatings.values()) {
                sum += rating;
            }
            double average = sum / itemRatings.size();
            userAverageRatings.put(userId, average);
        }

        // 将没有评分的用户用平均分插入到List<RelateDTO>中
        Set<Integer> allUsers = new HashSet<>();
        Set<Integer> allItems = new HashSet<>();
        for (RelateDTO dto : inputList) {
            allUsers.add(dto.getUseId());
            allItems.add(dto.getItemId());
        }

        List<RelateDTO> resultList = new ArrayList<>(inputList);
        for (int userId : allUsers) {
            for (int itemId : allItems) {
                if (!userItemRatings.containsKey(userId) || !userItemRatings.get(userId).containsKey(itemId)) {
                    double averageRating = userAverageRatings.getOrDefault(userId, 0D);
                    RelateDTO newDTO = new RelateDTO(userId, itemId, averageRating);
                    resultList.add(newDTO);
                }
            }
        }

        return resultList;
    }
```



## 如何混合基于内容的推荐和基于用户或物品的协同过滤推荐（对于冷启动问题）

该系统对于新用户或新物品冷启动问题，采用基于用户的协同过滤和基于内容的推荐的切换式混合推荐，以及基于物品的协同过滤和基于内容的推荐的线性加权式混合推荐，线性加权基于内容的推荐占0.3，基于物品的占0.7，具体代码实现加权式：

```
package com.springboot.recommend;

import com.springboot.sys.core.ItemCF;
import com.springboot.sys.entity.Item;
import com.springboot.sys.entity.RelateDTO;
import com.springboot.sys.entity.RelicInformation;

import java.util.*;
import java.util.stream.Collectors;

/**混合推荐
 * @Description
 * @Author zwf
 * @Date 2023/11/22 9:23
 */
public class WeightedHybridRecommender {

    /**
     * 猜你喜欢 （需要给出物品id） 加权式混合推荐
     * @param relicInformations 文物数据表
     * @param relateDTOS 评分数据表
     * @param itemId 基于id为itemId的文物
     * @param getNum 得到N个推荐物品
     * @return
     */
    public static List<Item> hybridRecommend(List<RelicInformation> relicInformations,List<RelateDTO> relateDTOS,Integer itemId,Integer getNum){
        //基于物品
        Map<Integer,Double> itemCFList = ItemCF.recommend(itemId,relateDTOS);

        // 基于内容的推荐
        Map<String, Double> itemCBList  = OfflineRecommend.recommend(relicInformations.size(), relicInformations, itemId);


        // 混合推荐的实现代码，采用线性加权的方式
        List<Item> hybridList = new ArrayList<>();
        double hybridScore;
        //物品推荐占7
        double itemWeight=0.7D;
        //内容推荐占3
        double contentWeight = 0.3D;
        //先把所有的基于内容的物品相似度放入,解决新物品冷启动问题
        for (Map.Entry<String, Double> stringDoubleEntry : itemCBList.entrySet()) {
            hybridScore=itemWeight*stringDoubleEntry.getValue();
            hybridList.add(new Item(stringDoubleEntry.getKey().toString(),hybridScore));
        }
        //删除之前有的，然后添加混合过后的
        for (Map.Entry<Integer, Double> integerDoubleEntry : itemCFList.entrySet()) {
            for (Map.Entry<String, Double> stringDoubleEntry : itemCBList.entrySet()) {

                if(integerDoubleEntry.getKey().toString().equals(stringDoubleEntry.getKey())) {
                    // 遍历列表并删除ID等于xxx的对象
                    Iterator<Item> iterator = hybridList.iterator();
                    while (iterator.hasNext()) {

                        if (iterator.next().getItemId().equals(stringDoubleEntry.getKey())) {
                            iterator.remove();
                        }
                    }
                    hybridScore = contentWeight * stringDoubleEntry.getValue() + itemWeight * integerDoubleEntry.getValue();
                    hybridList.add(new Item(stringDoubleEntry.getKey(), hybridScore));
                }
            }
        }


        // 根据得分排序推荐结果列表并返回前N个物品作为推荐结果（假设N为10）
        Collections.sort(hybridList, new Comparator<Item>() {
            @Override
            public int compare(Item item1, Item item2) {
                double score1 = item1.getScore();
                double score2 = item2.getScore();
                return Double.compare(score2, score1);
            }
        });
        return hybridList.subList(0,getNum<=hybridList.size()?getNum:hybridList.size());
    }
}

```

对于什么是混合推荐以及混合推荐有哪些方式[6][7]

## 参考文献或文章

[1]杨博;赵鹏飞.推荐算法综述[J].山西大学学报(自然科学版),2011,34(03):337-350.DOI:10.13451/j.cnki.shanxi.univ(nat.sci.).2011.03.001

[2]黄承慧;印鉴;侯昉.一种结合词项语义信息和TF-IDF方法的文本相似度量方法[J].计算机学报,2011,34(05):856-864.

[3]

[JAVA推荐系统-基于用户和物品协同过滤的电影推荐_洛阳泰山的博客-CSDN博客](https://blog.csdn.net/weixin_40986713/article/details/128922838?ops_request_misc=%7B%22request%5Fid%22%3A%22170075242716800184117626%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=170075242716800184117626&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-7-128922838-null-null.nonecase&utm_term=推荐&spm=1018.2226.3001.4450)

[4]孙小华.协同过滤系统的稀疏性与冷启动问题研究[D].浙江大学,2005.

[5]陈清浩.基于SVD的协同过滤推荐算法研究[D].西南交通大学,2015.

[6]彭余辉;张小雷;孙刚.基于内容和协同过滤加权融合的音乐推荐算法[J].安庆师范大学学报(自然科学版),2021,27(02):44-48+53.DOI:10.13757/j.cnki.cn34-1328/n.2021.02.009

[7]王淼;李大为.基于内容与协同过滤的混合推荐算法在数字科技馆中的应用[J].网络安全技术与应用,2023,(08):37-39.