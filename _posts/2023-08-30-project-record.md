---
layout: post
title: spring boot  + vue项目记录
date: 2023-09-01 16:59:00 +0800
category: Web
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/project.jpg
icon: web
---


* content
{:toc}
# spring boot  + vue项目记录 前端
## class中的字符串拼接
class="`el-icon-${item.icon}`",item是循环变量
## 高度
Vh:1vh 等于视口高度的1%

## 路由
$route 表示当前路由

$router.push 表示要去的路由操作

item.path表示要去的位置
## Flex弹性盒子布局
[flex布局教程](https://www.runoob.com/w3cnote/flex-grammar.html)

常用：1.display: flex;

  2.justify-content: space-between;

  3.align-items: center;

解释：弹性布局，主要是为了container中的两个布局均匀分开在一行

1.说明使用弹性布局

2.主要时水平方向

3.主要是竖直方向

## 组件大小
通过调整size来调整一些组建的大小

webstorm格式化代码快捷键 ctrl+alt+L

## 组件之间值的传递
使用vuex

一般来说store中常用的方法有
state,mutation actions

前面两个最常用，最后一个使用来异步处理

事件类型和回调函数 mutations里面的方法必须是同步的，异步用actions

调用mutation中的方法 通过store.commit('方法名')

vuex中使用modul将store中的多个存储对象分割为单个的方便管理和使用

##echarts在vue中的使用
1.在你需要导入的地方加上class 获取ref

2.使用var myChart = echarts.init(this.$refs.echarts1)

3.加入表格中的数据
var option1 = {
      title: {
        text: 'ECharts 入门示例'
      },
      tooltip: {},
      legend: {
        data: ['销量']
      },
      xAxis: {
        data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
      },
      yAxis: {},
      series: [
        {
          name: '销量',
          type: 'bar',
          data: [5, 20, 36, 10, 10, 20]
        }
      ]
    };

4.使用刚指定的配置项和数据显示图表。

 myChart.setOption(option1);

### vuex中...mapxxx的使用
```
...mapMutations([  
        'INCREASE_SHOPCART', //将mutation里的方法映射到该组件内
        'DECREASE_SHOPCART'  //等同于this.$store.commit('DECREASE_SHOPCART')  
      ]),

```
# 后端
## fastdfs
[详细了解FastDfs点击链接](https://juejin.cn/post/6942449134846607374)

##使用redis做缓存

## 为啥开发中需要多种环境的配置文件，不直接用一个
一开始，我也那么想，确实可以那么干，只是在上到服务器的时候，有些文件存放可能需要专门去yml文件改到服务器上的路径，这一来二去的操作，就变的麻烦了，所有加上额外这些配置文件，不管是开发还是生产环境都是有一定好处的
> 默认的一个，就是啥都不加的，默认配置文件，application.yml文件，可根据自己需求使用

> 开发环境（dev)：开发环境是专门用于开发的服务器，可随时配置，只要配的自己开发舒服就行

>测试环境(test)：一般是克隆一份生产环境的配置，一个程序在测试环境工作不正常，那么肯定不能把它发布到生产机上。
>
>生产环境（prod）：是正式提供对外服务的，一般会关掉错误报告，打开错误日志。这个配置就需要谨慎点哦
>
三个环境也可以说是系统开发的三个阶段：开发->测试->上线，其中生产环境也就是通常说的真实环境。

###好处
1.不同环境配置可以配置不同的参数

2.便于部署，提高效率，减少出错

## springboot如何整合swagger以及配置mybatisplus代码生成器

创建以下代码目录


[点击图片](https://img-blog.csdnimg.cn/ccf9621f989c44d68c79e5cd30a71de5.png)


### 整合swagger
model下面的DictBean

```
import lombok.Data;

/**
 * 字典bean
 * 只有code和text，可用于展示下拉框
 *
 * @author 波哥
 */
@Data
public class DictBean<T> implements IDict<T> {
    private final T code;
    private final String text;
}
```
<hr>
model下面的IDict
```
import java.lang.reflect.Field;
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.function.Function;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * 字典接口
 * <p>
 * 自定义的字典枚举类实现本接口后可省略属性code和text，以及对应的get方法
 * 在构造方法中只需调用init方法即可初始化
 *
 * @author 波哥
 */
public interface IDict<T> {
    /**
     * 通过code获取value
     *
     * @param clazz 枚举class
     * @param code  code
     * @return text
     */
    static <T> String getTextByCode(Class<? extends IDict<T>> clazz, T code) {
        return Stream.of(clazz.getEnumConstants())
                .filter((IDict<T> e) -> e.getCode().equals(code))
                .map(IDict::getText)
                .findAny().orElse(null);
    }

    /**
     * 通过text获取code
     *
     * @param clazz 枚举class
     * @param text  text
     * @return code
     */
    static <T> T getCodeByText(Class<? extends IDict<T>> clazz, String text) {
        return Stream.of(clazz.getEnumConstants())
                .filter((IDict<T> e) -> e.getText().equals(text))
                .map(IDict::getCode)
                .findAny().orElse(null);
    }

    /**
     * 通过code获取字典枚举实例
     *
     * @param clazz 枚举class
     * @param code  code
     * @param <T>   字典code类型
     * @param <R>   枚举类型
     * @return 字典枚举实例
     */
    @SuppressWarnings("unchecked")
    static <T, R extends IDict<T>> R getByCode(Class<? extends IDict<T>> clazz, T code) {
        return Stream.of(clazz.getEnumConstants())
                .filter((IDict<T> e) -> (e.getCode().equals(code)))
                .map(v -> (R) v)
                .findAny()
                .orElse(null);
    }

    /**
     * 获取给定的字典枚举项（常用下拉框数据请求）
     *
     * @param enums 可指定需要哪些项
     * @return List
     */
    @SafeVarargs
    static <T, E extends IDict<T>> List<IDict<T>> getItems(E... enums) {
        return Stream.of(enums)
                .map(DictPool::getDict)
                .collect(Collectors.toList());
    }

    /**
     * 获取所有字典枚举项，除开指定的枚举
     *
     * @param exclude 指定排除的枚举
     * @return List
     */
    @SafeVarargs
    @SuppressWarnings("unchecked")
    static <T, E extends IDict<T>> List<IDict<T>> getItemsExclude(E... exclude) {
        Class<IDict<T>> clazz = (Class<IDict<T>>) exclude.getClass().getComponentType();
        IDict<T>[] allEnum = clazz.getEnumConstants();
        List<IDict<T>> excludeList = Arrays.asList(exclude);
        return Stream.of(allEnum)
                .filter(e -> !excludeList.contains(e))
                .map(DictPool::getDict)
                .collect(Collectors.toList());
    }

    /**
     * 获取所有字典枚举项（常用下拉框数据请求）
     * 枚举值上标记@Deprecated的不会返回
     *
     * @param clazz 字典枚举类
     * @return List
     */
    static <T> List<IDict<T>> getAll(Class<? extends IDict<T>> clazz) {
        Map<String, Field> fieldCache = Arrays.stream(clazz.getDeclaredFields()).
                filter(Field::isEnumConstant).
                collect(Collectors.toMap(Field::getName, Function.identity()));
        IDict<T>[] allEnum = clazz.getEnumConstants();
        return Stream.of(allEnum)
                .filter(e -> !fieldCache.get(((Enum<?>) e).name()).isAnnotationPresent(Deprecated.class))
                .map(DictPool::getDict)
                .collect(Collectors.toList());
    }

    /**
     * 初始化
     *
     * @param code 字典编码
     * @param text 字典文本
     */
    default void init(T code, String text) {
        DictPool.putDict(this, code, text);
    }

    /**
     * 获取编码
     *
     * @return 编码
     */
    default T getCode() {
        return DictPool.getDict(this).getCode();
    }

    /**
     * 获取文本
     *
     * @return 文本
     */
    default String getText() {
        return DictPool.getDict(this).getText();
    }


    @SuppressWarnings("all")
    class DictPool {
        private static final Map<IDict, DictBean> DICT_MAP = new ConcurrentHashMap<>();

        private static final Map<String, Class<? extends IDict>> DICT_NAME_CLASS_MAP = new ConcurrentHashMap<>();

        static <T> void putDict(IDict<T> dict, T code, String text) {
            DICT_NAME_CLASS_MAP.put(dict.getClass().getName(), dict.getClass());
            DICT_MAP.put(dict, new DictBean<>(code, text));
        }

        public static List<IDict<Object>> getDict(String dictName) {
            Class<? extends IDict> aClass = DICT_NAME_CLASS_MAP.get(dictName);
            return IDict.getAll((Class<? extends IDict<Object>>) aClass);
        }

        static <K extends IDict<T>, T> DictBean<T> getDict(K dict) {
            return DICT_MAP.get(dict);
        }


    }

}

```

ApiModelPropertyEnum文件
```
import com.wanshu.common.model.IDict;

import java.lang.annotation.*;

/**
 * Swagger文档注释枚举翻译
 */
@Documented
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ApiModelPropertyEnum {

    /**
     * 属性对应的字段枚举
     */
    Class<? extends IDict<?>> dictEnum();

}
```

EnumPropertyBuilderPlugin
```
import com.fasterxml.jackson.databind.introspect.AnnotatedField;
import com.fasterxml.jackson.databind.introspect.BeanPropertyDefinition;

import com.wanshu.common.model.IDict;
import io.swagger.annotations.ApiModelProperty;
import lombok.extern.slf4j.Slf4j;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.schema.ModelPropertyBuilderPlugin;
import springfox.documentation.spi.schema.contexts.ModelPropertyContext;
import springfox.documentation.spi.service.ExpandedParameterBuilderPlugin;
import springfox.documentation.spi.service.contexts.ParameterExpansionContext;

import java.util.Arrays;
import java.util.Optional;
import java.util.stream.Collectors;

/**
 * Swagger枚举属性增强插件
 *
 * @author 波哥
 * @date 2022-5-10
 */

@Slf4j
public class EnumPropertyBuilderPlugin implements ModelPropertyBuilderPlugin, ExpandedParameterBuilderPlugin {


    /**
     * 枚举类型属性自动扩展注释
     * 适用于非get请求接口中DTO的属性（有注解@RequestBody标识）
     *
     * @param context context
     */
    @Override
    public void apply(ModelPropertyContext context) {
        context.getBeanPropertyDefinition()
                .map(BeanPropertyDefinition::getField)
                .map(AnnotatedField::getAnnotated)
                .ifPresent(field -> {
                    ApiModelPropertyEnum apiModelPropertyEnum = field.getAnnotation(ApiModelPropertyEnum.class);
                    ApiModelProperty apiModelProperty = field.getAnnotation(ApiModelProperty.class);
                    if (apiModelPropertyEnum != null && apiModelProperty != null) {
                        SwaggerUtils.addTo(context, this.getDescription(apiModelPropertyEnum));
                    }
                });
    }

    /**
     * 枚举类型属性自动扩展注释
     * 适用于get请求参数中DTO的属性
     *
     * @param context context
     */
    @Override
    public void apply(ParameterExpansionContext context) {
        Optional<ApiModelProperty> apiModelPropertyOptional = context.findAnnotation(ApiModelProperty.class);
        Optional<ApiModelPropertyEnum> apiModelPropertyEnumOptional = context.findAnnotation(ApiModelPropertyEnum.class);
        if (apiModelPropertyOptional.isPresent() && apiModelPropertyEnumOptional.isPresent()) {
            ApiModelPropertyEnum apiModelPropertyEnum = apiModelPropertyEnumOptional.get();
            context.getRequestParameterBuilder().description(this.getDescription(apiModelPropertyEnum));
        }
    }

    @Override
    public boolean supports(DocumentationType documentationType) {
        return true;
    }

    /**
     * 增强注释，将枚举信息添加到原注释之后
     */
    private String getDescription(ApiModelPropertyEnum apiModelPropertyEnum) {
        Class<? extends IDict<?>> dictEnum = apiModelPropertyEnum.dictEnum();
        return Arrays.stream(dictEnum.getEnumConstants()).map(dict -> dict.getCode() + "：" + dict.getText()).collect((Collectors.joining("，")));
    }


}

```

SwaggerConfiguration

```
import io.swagger.annotations.Api;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.oas.annotations.EnableOpenApi;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;

/**
 * Swagger配置
 *
 * @author lgy
 */
@Slf4j
@Configuration
@EnableOpenApi
public class SwaggerConfiguration {

    @Value("${spring.application.name}")
    private String applicationName;


    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title(applicationName)
                .description(applicationName + "接口文档。")
                .contact(new Contact("婉舒CRM系统", "", "dengpbs@163.com"))
                .version("1.0")
                .build();
    }

    @Bean
    public EnumPropertyBuilderPlugin enumPropertyBuilderPlugin() {
        return new EnumPropertyBuilderPlugin();
    }


    @Bean
    public ValidatePropertyBuilderPlugin validatePropertyBuilderPlugin() {
        return new ValidatePropertyBuilderPlugin();
    }

    @Bean
    public SwaggerConsole swaggerConsole() {
        return new SwaggerConsole();
    }
}

```

SwaggerConsole

```
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.core.env.Environment;

import javax.annotation.Resource;
import java.net.InetAddress;
import java.util.Optional;

/**
 * @author liao
 */
@Slf4j
public class SwaggerConsole implements ApplicationRunner {

    @Resource
    private Environment environment;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        String appName = environment.getProperty("spring.application.name");
        String contextPath = Optional.ofNullable(environment.getProperty("server.servlet.context-path")).orElse("");
        String host = InetAddress.getLocalHost().getHostAddress();
        String port = Optional.ofNullable(environment.getProperty("server.port")).orElse("8080");
        log.info("\n----------------------------------------------------------\n" +
                "\tApplication '{}' is running! \n" +
                "\tSwagger Doc:  http://{}:{}{}/doc.html\n" +
                "----------------------------------------------------------", appName, host, port, contextPath);
    }

}

```

SwaggerShortcutController
```
import org.apache.commons.lang3.StringUtils;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author 波哥
 */
@Controller
public class SwaggerShortcutController {

    private final Environment environment;

    public SwaggerShortcutController(Environment environment) {
        this.environment = environment;
    }

    @GetMapping
    public void index(HttpServletResponse response) throws IOException {
        String contextPath = environment.getProperty("server.servlet.context-path");
        response.sendRedirect((StringUtils.isNotEmpty(contextPath) ? contextPath : StringUtils.EMPTY).concat("/doc.html"));
    }

}
```

SwaggerUtils

```
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import springfox.documentation.builders.PropertySpecificationBuilder;
import springfox.documentation.spi.schema.contexts.ModelPropertyContext;

import java.lang.reflect.Field;
import java.util.Objects;

/**
 * 工具类
 */
@Slf4j
public class SwaggerUtils {

    private SwaggerUtils() {
    }

    public static void addTo(ModelPropertyContext context, String message) {
        if (StringUtils.isBlank(message)) {
            return;
        }
        PropertySpecificationBuilder specificationBuilder = context.getSpecificationBuilder();
        try {
            Field descriptionField = specificationBuilder.getClass().getDeclaredField("description");
            descriptionField.setAccessible(true);
            Object description = descriptionField.get(specificationBuilder);
            if (Objects.nonNull(description) && StringUtils.isNotBlank(description.toString())) {
                specificationBuilder.description(description + "  (" + message + ")");
            }
        } catch (NoSuchFieldException | IllegalAccessException e) {
            log.error("swagger追加描述失败", e);
        }
    }


}

```

ValidatePropertyBuilderPlugin
```
import com.fasterxml.jackson.databind.introspect.AnnotatedField;
import com.fasterxml.jackson.databind.introspect.BeanPropertyDefinition;
import lombok.extern.slf4j.Slf4j;
import org.hibernate.validator.internal.engine.messageinterpolation.InterpolationTermType;
import org.hibernate.validator.internal.engine.messageinterpolation.parser.TokenCollector;
import org.hibernate.validator.internal.engine.messageinterpolation.parser.TokenIterator;
import org.hibernate.validator.resourceloading.PlatformResourceBundleLocator;
import org.springframework.core.annotation.AnnotationUtils;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.schema.ModelPropertyBuilderPlugin;
import springfox.documentation.spi.schema.contexts.ModelPropertyContext;

import javax.validation.Constraint;
import java.lang.annotation.Annotation;
import java.util.Locale;
import java.util.Map;
import java.util.ResourceBundle;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * Swagger基于validation注解生成 提示文档
 *
 */
@Slf4j
public class ValidatePropertyBuilderPlugin implements ModelPropertyBuilderPlugin {


    private final PlatformResourceBundleLocator platformResourceBundleLocator = new PlatformResourceBundleLocator("org.hibernate.validator.ValidationMessages");

    private final ResourceBundle defaultResourceBundle = platformResourceBundleLocator.getResourceBundle(Locale.CHINA);

    @Override
    public void apply(ModelPropertyContext context) {
        try {
            context.getBeanPropertyDefinition()
                    .map(BeanPropertyDefinition::getField)
                    .map(AnnotatedField::getAnnotated)
                    .ifPresent(field -> {
                        Annotation[] annotations = field.getAnnotations();
                        String message = Stream.of(annotations)
                                .filter(annotation -> annotation.annotationType().isAnnotationPresent(Constraint.class))
                                .map(this::annotationToMessage).collect(Collectors.joining(","));
                        SwaggerUtils.addTo(context, message);
                    });
        } catch (Exception e) {
            log.error("追加validation 注解描述失败", e);
        }
    }

    private String annotationToMessage(Annotation annotation) {
        Map<String, Object> annotationAttributes = AnnotationUtils.getAnnotationAttributes(annotation);
        String message = annotationAttributes.get("message").toString();
        if (message.indexOf("{") != 0) {
            return message;
        }
        String resolvedMessage = defaultResourceBundle.getString(removeCurlyBraces(message));
        TokenIterator tokenIterator = new TokenIterator(new TokenCollector(resolvedMessage, InterpolationTermType.PARAMETER).getTokenList());
        while (tokenIterator.hasMoreInterpolationTerms()) {
            String term = tokenIterator.nextInterpolationTerm();
            String resolvedExpression = annotationAttributes.get(removeCurlyBraces(term)).toString();
            tokenIterator.replaceCurrentInterpolationTerm(resolvedExpression);
        }
        return tokenIterator.getInterpolatedMessage();
    }

    private String removeCurlyBraces(String parameter) {
        return parameter.substring(1, parameter.length() - 1);
    }

    @Override
    public boolean supports(DocumentationType documentationType) {
        return true;
    }


}

```


### 整合mybatisplus代码生成以及分页

MybatisPlusConfiguration

```
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Description 快速生成mybatis代码的配置文件
 * @Author zwf
 * @Date 2023/8/30 14:58
 */
@Configuration
@MapperScan(basePackages = {"com.wanshu.sys.mapper"})
public class MybatisPlusConfiguration {
    /**
     * 新的分页插件,一缓和二缓遵循mybatis的规则,
     * 需要设置 MybatisConfiguration#useDeprecatedExecutor = false 避免缓存出现问题(该属性会
     在旧插件移除后一同移除)
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

MyFastGeneratorConfiguration

```
import com.baomidou.mybatisplus.generator.FastAutoGenerator;
import com.baomidou.mybatisplus.generator.config.OutputFile;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.Collections;


/**
 * @Description 代码生成器
 * @Author zwf
 * @Date 2023/8/30 15:09
 */
public class MyFastGeneratorConfiguration {
    public static void main(String[] args) {
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/wanshu_crm? serverTimezone = UTC & useUnicode = true & characterEncoding = utf-8 & useSSL = true"
                        , "root", "root")
                .globalConfig(builder -> {
                    builder.author("zhangsan") // 设置作者
                            .enableSwagger() // 开启 swagger 模式
                            .fileOverride() // 覆盖已生成文件
                            .outputDir("D:\\WSCRM\\src\\main\\java"); // 指定输出目录
                })
                .packageConfig(builder -> {
                    builder.parent("com.wanshu") // 设置父包名
                            .moduleName("sys") // 设置父包模块名
                            .pathInfo(Collections.singletonMap(OutputFile.xml,
                                    "D:\\WSCRM\\src\\main\\resources\\mapper")); // 设置mapperXml生成路径
                })
                .strategyConfig(builder -> {
                    builder.addInclude("sys_role") // 设置需要生成的表名
                //.addTablePrefix("act_fo_")
                    ; // 设置过滤表前缀
                })
                .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模 板，默认的是Velocity引擎模板
                .execute();
    }
}

```
<hr>

###restcontroller和controller区别
restcontroller的返回值是json格式

controller返回的是静态html

##resultwrapper对返回结果的封装
1.可以帮助我们自动封装controller的返回集合

2.也可以不使用，其中有自定义注释

3.也可以使用自定义返回值
