+++
title = "vscode 自定义代码片段"
date = 2019-03-09T17:54:58+08:00
tags = ["IDE","工欲善其事，必先利其器"]
categories = ["IDE","工欲善其事，必先利其器"]
draft = false
commentId="ide-vs-code-snippet"
+++

# 实现效果
![](http://pic.artacode.com/1.gif)
# 起因
最近在写一个全新的项目，在项目中频繁创建各种类，这就导致很多重复的东西需要频繁的写，例如类名，命名空间，继承关系...那么有没有一种 办法能解决这个问题呢？  
# 提出设想
我想起了，最初用 `sublime text` 的时候，可以利用代码片段功能大段的生成html代码，当时就觉得十分的方便，那么 `vscode` 有没有这个功能呢?经过 `google` 之后我知道 `vscode` 是有代码片段功能的。既然有了想法，也具备了基础实施条件，那么接下来开始尝试实现之前的想法。
# 资料查询
经过一番 `google` 后发现对于 `vscode snippet` 介绍都在相对基础的简单应用（只是一些插入固定代码和光标介绍），者显然无法实现我们生成类名和明明空间的想法，`google` 无果，那么只能看看 `vscode` 官方文档果然有意想不到的收获，看完官网介绍后，基本就确定此路是可行的。

# snippet 示例
在 `File > Preferences (Code > Preferences on macOS)` 中选择 `User Snippets` 在弹出框里选择对应的代码片段语言，我这里使用的是php

![](http://pic.artacode.com/20190310095020.png)

```
 "Print to console": {
	 	"prefix": "log",
	 	"body": [
	 		"console.log('$1');",
	 		"$2"
	 	],
 	    "description": "Log output to console"
	}
```
在打开的 `php.json` 中有示例代码：
- `Print to console` 代码片段名称
- `prefix` 插件前缀
- `body` 插件内容可以是字符串，也可以为数组，若为数组每个元素都做为单独的一行插入。
- `description` 插件描述

# Snippet 语法
## 制表位(Tabstops)
使用制表位(Tabstops)可是在代码片段中移动光标位置，使用$1,$2来指定光标的位置,数字代表光标的移动的顺序，值得注意的时$0代表光标的最后位置。如果有多个相同的制表位(Tabstops)会在编译器里同时出现多个光标（类似编译器的块编辑模式）。
## 占位符(Placeholders)
占位符(Placeholders) 是带默认值的制表位(Tabstops),占位符(Placeholders)的文本会被插入到制表位(Tabstops)所在位置并且全选以方便修改,占位符(Placeholders)可以嵌套使用，比如`${1:another ${2:placeholder}}`。
## 选择项(Choice)
占位符(Placeholders)可以有多选值，每个选项的值用 `,` 分隔，选项的开始和结束用管道符号(`|`)将选项包含，例如: `${1|one,two,three|}`，当插入代码片段，选择制制表位(Tabstops)的时候，会列出选项供用户选择。
## 变量(Variables)
使用 `$name` 或者 `${name|default}` 可以插入变量的值，如果变量未被赋值则插入 `default` 的值或者空值 。当变量未被定义，则将变量名插入，变量(Variables)将被转换为占位符(Placeholders)
系统变量如下
- `TM_SELECTED_TEXT` 当前选定的文本或空字符串
- `TM_CURRENT_LINE` 当前行的内容
- `TM_CURRENT_WORD` 光标下的单词的内容或空字符串
- `TM_LINE_INDEX` 基于零索引的行号
- `TM_LINE_NUMBER` 基于一索引的行号
- `TM_FILENAME` 当前文档的文件名
- `TM_FILENAME_BASE` 当前文档的文件名（不含后缀名)
- `TM_DIRECTORY` 当前文档的目录
- `TM_FILEPATH ` 当前文档的完整文件路径
- `CLIPBOARD` 剪切板里的内容
插入当前日期或时间：
- `CURRENT_YEAR` 当前年(四位数)
- `CURRENT_YEAR_SHORT` 当前年(两位数)
- `CURRENT_MONTH` 当前月
- `CURRENT_MONTH_NAME` 本月的全名（’七月’）
- `CURRENT_MONTH_NAME_SHORT` 月份的简称（’Jul’）
- `CURRENT_DATE` 当前日
- `CURRENT_DAY_NAME` 当天的名称（’星期一’）
- `CURRENT_DAY_NAME_SHORT` 当天的短名称（’Mon’）
- `CURRENT_HOUR` 当前小时
- `CURRENT_MINUTE` 当前分钟
- `CURRENT_SECOND` 当前秒
当前语言的行注释或块注释:
- `BLOCK_COMMENT_START` 块注释开始标识,如 PHP `/* ` 或 HTML `<!--`
- `BLOCK_COMMENT_END` 块注释结束标识,如 PHP `*/ ` 或 HTML `-->`
- `LINE_COMMENT` 行注释，如： PHP `//` 或 HTML `<!-- -->` 
下面片则会生成 PHP /* Hello World */
```
{
    "hello": {
        "scope": "php",
        "prefix": "hello",
        "body": "$BLOCK_COMMENT_START Hello World $BLOCK_COMMENT_END"
    }
}
```

## 变量转换(Variable transforms)
变量转换(Variable transforms) 允许变量在插入前改变变量的值，变量转换(Variable transforms)由三部分组成
1. 正则匹配：使用正则表达式匹配变量值，若变量无法解析则值为空。
2. 格式串：允许引用正则表达式匹配组。格式串允许条件插入和做简单的修改。
3. 正则表达式匹配选项

下面例子是使用变量转换(Variable transforms)将带后缀的文件名转换为不带后缀的文件名
```
${TM_FILENAME/(.*)\\..+$/$1/}
  |           |         |  |
  |           |         |  |-> 无选项设置
  |           |         |
  |           |         |-> 引用捕获组的第一个分组内容
  |           |             
  |           |
  |           |-> 匹配后缀前的所有字符串
  |               
  |
  |-> 文件名（带后缀）
```

# 需求实现
## 要解决的问题
生成Class 的命名空间、类名、选择继承关系

## 问题分析
项目目录结构如下所示
```
peoject
|
|----application
|--------admin
|------------services
|----------------TestServices.php
```
类名可以直接使用 `TM_FILENAME_BASE` 变量的值即可，命名空间则需要使用 `TM_DIRECTORY` 变量，以 `TestServices.php` 为例，`TM_DIRECTORY` 得到的目录为 `peoject\application\admin\services`我们只需要将`peoject\application\` 替换为 `app` 得到 `app\admin\services` 就是我们的明明空间了，至于继承就是一个选择项就可以了。既然已经全部知道该如何实现，接下来就是代码实现的过程了。

## 代码实现
```
"service-construct" :{
		"prefix": "gen",
		"body": [
			"namespace ${TM_DIRECTORY/.*application/app/};\n",
			"class $TM_FILENAME_BASE extends ${1|BaseService,BaseController,BaseModel|}",
			"{",
			"\tpublic function __construct() \r\n    {\n\t\t\\$this->model = new \r\n\t}",
			"}"
		],
		"description": "generate service class"

	},
```

# 一些思考
上述代码基本上完成了我要实现的功能，但是也存在了一些问题，例如：我现在是用 `windows` 操作系统，因而`TM_DIRECTORY` 得到的目录为 `peoject\application\admin\services` 若是 `linux` 系统，此代码片段是无法正常的生成命名空间的，我做了一些资料的搜索，代码片段并没有自定义变量的功能（也许可以，只是我么有找到方法，如果有知道的大牛，请留言执教。  
随着对 `vscode snippet` 的深入了解，我之前所设想的方案要用代码片段的方式实现是有些困难的，`vscode` 将其作为一个快速生成代码的解决方案，我们所写的代码片段相当于一个带有填空模板，而对代码片段的应用就是生成带有制表符的代码模板，然后根据制表符顺序补全代码。  
至于这个不完美的方案，我打算再研究一下代码片段是否能完全实现，如果依旧解决不了，就选用其它方案进行尝试。