+++
title = "gorm常见问题"
date = 2018-12-22T10:38:17+08:00
tags = ["golang"]
categories = ["golang"]
draft = false
commentId = "gorm-FAQ"
+++
# sql: Scan error on column index 5, name "created_at": unsupported Scan, storing driver.Value type []uint8 into type *time.Time

DATETIME转换问题
执行Rows.Scan()时报告错误

原因是在调用sql.Open()时没有将parseTime设置为True。加入parseTime即可修复问题：
`db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/dbname?parseTime=true")`

# invalid memory address or nil pointer dereference on simple learning example. Maybe a bug with latest version
代码实例
```
package main

import (
	"net/http"
	"strconv"

	"github.com/gin-gonic/gin"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/postgres"
)

var db *gorm.DB

func init() {
	var err error
	db, err := gorm.Open("postgres", "host=localhost user=postgres dbname=postgres sslmode=disable password=pass")
	if err != nil {
		panic("failed to connect database")
	}
	db.AutoMigrate(&todoModel{})
}
```
重点在 `	db, err := gorm.Open("postgres", "host=localhost user=postgres dbname=postgres sslmode=disable password=pass")` 代码处，由于上述代码中已经设置了全局变量 `db` 所以在接受赋值的时候不能用 `:=` 二应该用 `=` 。  
正确代码应该为 `db, err := gorm.Open("postgres", "host=localhost user=postgres dbname=postgres sslmode=disable password=pass")`