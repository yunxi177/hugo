+++
title = "vs code 自定义代码片段"
date = 2019-03-08T17:54:58+08:00
tags = ["IDE"]
categories = ["IDE"]
draft = true
commentId=ide-vs-code-snippet
+++

```
{
	// Place your snippets for php here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }

	"adminmodel": {
		"prefix": "space",
		"body": [
			"namespace app\\admin\\model;",
		],
		"description": "admin model namespace"
	},
	"validate" :{
		"prefix": "space",
		"body": [
			"namespace app\\validate;",
		],
		"description": "validate namespace"

	},
	"enum" :{
		"prefix": "space",
		"body": [
			"namespace app\\enum;",
		],
		"description": "validate namespace"

	},
	"services" :{
		"prefix": "space",
		"body": [
			"namespace app\\admin\\services;",
		],
		"description": "validate namespace"

	},
	"contro" :{
		"prefix": "space",
		"body": [
			"namespace app\\admin\\controller;",
		],
		"description": "validate namespace"

	},
	"service-construct" :{
		"prefix": "gen",
		"body": [
			"namespace ${TM_DIRECTORY/.*application/app/};\n",
			"class $TM_FILENAME_BASE extends ${1|BaseService,BaseController,BaseModel|}",
			"{",
			"\tpublic function __construct() \r\n    {\n\t\t\\$this->model = new \r\n\t}",
			"}"
		],
		"description": "validate namespace"

	}
}
```