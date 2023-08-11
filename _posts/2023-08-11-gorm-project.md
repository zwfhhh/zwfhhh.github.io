---
layout: post
title: 使用 GORM（Go 的 ORM 库）连接数据库，并实现增删改查操作，把实现过程整理成文章（实操） | 青训营
date: 2023-08-11 08:23:00 +0800
category: Go
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/go.jpg
icon: code
---


* content
{:toc}
# 使用 GORM（Go 的 ORM 库）连接数据库，并实现增删改查操作，把实现过程整理成文章（实操） | 青训营
> 这是我参与「第六届青训营 -后端场」笔记创作活动的的第3篇笔记
## 1.创建一个结构体（javabean类比）
```
type User struct {

	gorm.Model

	Name      string `gorm:"type:varchar(20);not null"`

	Telephone string `gorm:"varchar(110);not null;unique"`

	Password  string `gorm:"size:255;not null"`
}
```
这里的gorm.Model包含了 gorm.Model 结构体，其包括字段 ID、CreatedAt、UpdatedAt、DeletedAt，使用起来非常方便
## 2.使用viper读取config配置文件
创建一个config目录，下面创建一个application.yml文件，里面配置
```
server:
  port: 8082

dataSource:
  driverName: "mysql"
  host: "localhost"
  port: "3306"
  database: "gormdb"
  username: "root"
  password: "root"
  charset: "utf8"
```
封装一个读取配置文件的方法
```
func InitConfig() {
	workDir, _ := os.Getwd()
	viper.SetConfigName("application")
	viper.SetConfigType("yml")
	viper.AddConfigPath(workDir + "/config")
	err := viper.ReadInConfig()
	if err != nil {
		panic(err)
	}
}
```
该方法是获取到当前位置的文件目录，设置要读取的文件的名字，以及相对路径
## 3.初始化数据的方法
```
func InitDB() *gorm.DB {

	driverName := viper.GetString("dataSource.driverName")
	host := viper.GetString("dataSource.host")
	port := viper.GetString("dataSource.port")
	database := viper.GetString("dataSource.database")
	username := viper.GetString("dataSource.username")
	password := viper.GetString("dataSource.password")
	charset := viper.GetString("dataSource.charset")
	args := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=%s&parseTime=True&loc=Local",
		username,
		password,
		host,
		port,
		database,
		charset)
	db, err := gorm.Open(driverName, args)
	if err != nil {
		panic("failed to connect database ,err=" + err.Error())
	}
	db.AutoMigrate(&User{})
	return db
}
```
这里面的参数大部分使用viper读取文件当中的数据，方便项目部署之后的修改，一般结构体bean对象映射到数据库都是在初始化数据库里面来做 db.AutoMigrate()
## main方法中
先初始化读取配置文件的设置
然后在初始化数据库
### create
```
u := &User{
		Name:      "李四",
		Password:  "122",
		Telephone: "111",
	}
	if err := db.Create(u).Error; err != nil {
		panic("错误")
	}
```
### update
```
err := db.Model(&User{}).Where("Name=?", "张三").Update("Telephone", "17380172762")
	if err != nil {
		panic("failed")
	}
```
### query
```
user1 := []User{}
	if err := db.Find(&user1); err != nil {
		fmt.Sprintf("err=%s", err.Error)
	} else {
		fmt.Printf("success")
	}
	for _, u := range user1 {
		fmt.Printf("username=" + u.Name)
	}
```
### delete
```
if err := db.Where("name=?", "张三").Delete(&User{}); err != nil {
		fmt.Sprintf("删除失败，%s", err.Error)
		return
	}
```
注意：gorm里面的删除都是软删除，删除之后数据库中的数据还在，不过gorm.Model里面的deleteAt字段数据会发生改变
## 问题
cannot use r.db (type *“gorm.io/gorm“.DB) as type *“github.com/jinzhu/gorm“.DB【Go、Gin】

### 解决
没有导入正确的包，应该导入
"github.com/jinzhu/gorm"