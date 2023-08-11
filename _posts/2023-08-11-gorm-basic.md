---
layout: post
title: Gorm 框架笔记 ｜ 青训营
date: 2023-08-11 08:22:00 +0800
category: Go
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/go.jpg
icon: note
---


* content
{:toc}
# Gorm 框架笔记 ｜ 青训营
> ### 这是我参与「第六届青训营 -后端场」笔记创作活动的的第3篇笔记
## 模型定义
创建结构体时候要绑定数据库中表的字段名

gorm标签语法： gorm:"标签定义"

指定列名：gorm:"column:createtime"

指定主键：gorm:"column:id; PRIMARY_KEY"

忽略字段：gorm:"-" 可以忽略struct字段，被忽略的字段不参与gorm的读写操作
### 模型定义表名
// 设置表名，可以通过给 Food struct 类型定义 TableName 函数，返回一个字符串作为表名
func (v Food) TableName() string {
	return "food"
}
### gorm.Model结构体
Gorm 定义一个 gorm.Model 结构体，其包括字段 ID、CreatedAt、UpdatedAt、DeletedAt。
## 连接数据库
### 1.配置dsn
dsn格式[username[:password]@][protocol[(address)]]/dbname[?param1=value1&...&paramN=valueN]
### 2.连接数据库
一般数据库连接参数，我们都是保存在配置文件里面，需要从配置文件加载参数，然后拼接 dsn。
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8&parseTime=True&loc=Local&timeout=%s", username, password, host, port, Dbname, timeout)

	// 连接 Mysql, 获得 DB 类型实例，用于后面的数据库读写操作。
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})

## 插入数据
一般项目中有一下写法：
```
if err := db.Create(&u).Error; err != nil {
	fmt.Println("插入失败", err)
	return
}
```
后来gorm在插入数据之后都会自动返回主键id
## 查询数据
### 查询方法
#### 1.Take
if err := db.Create(&u).Error;<br> err != nil {
	fmt.Println("插入失败", err)
	return
}
#### 2.First(常用)
// 等价于：SELECT * FROM `foods` ORDER BY `foods`.`id` ASC LIMIT 1  <br> 
db.First(&food)
#### 3.Last
// 等价于：SELECT * FROM `foods`  ORDER BY `foods`.`id` DESC LIMIT 1 <br>
db.Last(&food)
#### 4.Find
查询多条记录，Find函数返回的是一个数组<br>
// 等价于：SELECT * FROM `foods`
<br>db.Find(&foods)
#### 5.Pluck
查询一列值
//商品标题数组
var titles []string
<br>
// 返回所有商品标题<br>
// 等价于：SELECT title FROM `foods`<br>
// Pluck提取了title字段，保存到titles变量<br>
// 这里Model函数是为了绑定一个模型实例，可以从里面提取表名。<br>
db.Model(&Food{}).Pluck("title", &titles)
## 查询错误处理
通过db.Error属性判断查询结果是否出错，Error属性不等于nil
注意：当使用的sql中没有可以绑定表名的，可以直接使用db.Model（)来绑定
<br>
sql语句中的offset表示删除在最前面的两条数据，再进行操作
## 更新数据
### Save
db.Save(&food)
### Update
更新一条
// 等价于: UPDATE `foods` SET `price` = '25'  WHERE `foods`.`id` = '2'<br>
db.Model(&food).Update("price", 25)<br>
// 等价于: UPDATE `foods` SET `price` = '25'<Br>
db.Model(&Food{}).Update("price", 25)<Br>
// 因为 Food{} 的 id 为空，gorm库就不会以 id 作为条件，where 语句就是空的
### 更新表达式
UPDATE foods SET stock = stock + 1 WHERE id = '2'<br>
// 等价于: UPDATE `foods` SET `stock` = stock + 1  WHERE `foods`.`id` = '2'
db.Model(&food).Update("stock", gorm.Expr("stock + 1"))
## 删除数据
food := Food{}<Br>
// 先查询一条记录, 保存在模型变量 food<Br>
// 等价于: SELECT * FROM `foods`  WHERE (id = '2') LIMIT 1
db.Where("id = ?", 2).Take(&food)<br>

// 删除 food 对应的记录，通过主键 Id 标识记录<Br>
// 等价于： DELETE from `foods` where id=2;<Br>
db.Delete(&food)<Br>
### 通过where删除
db.Where("type = ?", 5).Delete(&Food{})
## 关联查询属于（包含）
通常用于描述一个 Model属于另外一个 Model，可以说每一条 profiles 记录都属于某个用户

总之：就是有两个表，其中一个表里面的字段类型是另一个表
```
type Profile struct {
  gorm.Model
  UserID uint // 外键
  // 定义 user 属性关联 users 表，默认情况使用 类型名 + ID 组成外键名，在这里 UserID 属性就是外键
  User   User
  Name   string
}
```
### 外键
关联属性类型 + 主键）组成外键名，如上面的例子 User + ID 组成 UserID，UserID 就作为 Profile 的外键
## 一对多
比如：一个 用户拥有多张信用卡
// 用户

type User struct {

// 继承gorm的基础Model,里面默认定义了ID、CreatedAt、UpdatedAt、DeletedAt 4个字段

gorm.Model

CreditCards []CreditCard // 一对多关联属性，表示多张信用卡

}

// 信用卡

type CreditCard struct {

gorm.Model

Number   string // 卡号

UserID  uint // 默认外键， 用户Id

}
### 查询
// 关联查询的结果，保存到user.CreditCard属性
db.Model(&user).Association("CreditCard").Find(&user.CreditCard)
## 自动建表 
这个功能类似于java中的逆向工程

通过 AutoMigrate 函数可以快速建表，如果表已经存在不会重复创建，同时AutiMigrate函数也可可以帮助我们把模型和数据库的表对应起来

总结：在执行我的sql语句之前我们总要把模型绑定表，否则不能执行