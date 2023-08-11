---
layout: post
title: Android项目打包为apk时候没有带上sqllite数据
date: 2023-08-10 23:20:00 +0800
category: Android
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/note.jpg
icon: code
---


* content
{:toc}
# android项目打包为apk时候没有带上sqllite数据

起因：安卓项目打包之后如果没有带上sqllite数据库，开始手机上是没有初始数据的,一些功能就无法继续测试

解决方法：

准备方法：

![image-20230603205711965](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230603205711965.png)

![image-20230603205837056](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230603205837056.png)

开始操作：

接下来的代码都是写在你的项目开始的位置，我的是登录的位置

```java
//你的数据库文件在模拟器中的位置,
//初始变量
String DB_PATH = "/data/data/com.example.foodorder/databases/";
```

![image-20230603205420110](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230603205420110.png)

```java
//数据库文件名字 
String DB_NAME = "数据库文件名字（FoodOrder3）";
```

![image-20230603205522661](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230603205522661.png)

将下面这个方法复制到你的项目开始活动中

```java

 private void cloneData() {
//        Log.e("TAG", "cloneData: ",new File(DB_PATH + DB_NAME)).exists() == false);
        if ((new File(DB_PATH + DB_NAME)).exists() == false) {
            Log.e("数据库是否存在", "cloneData: "+"不存在" );
            // 如 SQLite 数据库文件不存在，再检查一下 database 目录是否存在
            File f = new File(DB_PATH);
            // 如 database 目录不存在，新建该目录
            Log.e("目录路径", "cloneData: "+f );
            if (!f.exists()) {
                f.mkdir();
            }


            try {
                // 得到 assets 目录下我们实现准备好的 SQLite 数据库作为输入流
                InputStream is = getBaseContext().getAssets().open(DB_NAME);
                // 输出流
                OutputStream os = new FileOutputStream(DB_PATH + DB_NAME);


                // 文件写入
                byte[] buffer = new byte[1024];
                int length;
                while ((length = is.read(buffer)) > 0) {
                    os.write(buffer, 0, length);
                }


                // 关闭文件流
                os.flush();
                os.close();
                is.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }else {
            Log.e("数据库是否存在", "cloneData: 存在数据库");
            Log.e("存在目录", "cloneData: "+(new File(DB_PATH + DB_NAME)).getAbsolutePath());

//            /data/data/com.siyusoulmate.mm/databases/mm.db
        }

    }


```

