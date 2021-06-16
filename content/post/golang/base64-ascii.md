+++
title = "golang base64 编码与 PHP 输出不一致"
date = 2021-06-16T17:49:39+08:00
tags = ["php", "golang"]
categories = ["php", "golang"]
draft = false
commentId="golang-base64-ascii"

+++

最近开发中，将一个 php 算法，移植到 golang 中，发现 base64 算法生成的字符串不一致，经过排查发现是由于 ASCII 控制字符导致的原因，加下来看代码

```php
<?php
$asciiArr =[10, 187, 217, 12, 207, 183, 184, 231, 184, 149, 118, 151];

$str = '';
foreach ($asciiArr as $ascii) {
	$str .= chr($ascii);
}

echo base64_encode($str);
```

上述代码输出： `CrvZDM+3uOe4lXaX`



```go
package main

import (
	"encoding/base64"
	"fmt"
)

func main() {
	res := []int{10, 187, 217, 12, 207, 183, 184, 231, 184, 149, 118, 151}
	var str string
	var b []byte
	for _, v := range res {
		str += string(v)
		b = append(b, byte(v))
	}
	byteCode := base64.StdEncoding.EncodeToString([]byte(b))
	strCode := base64.StdEncoding.EncodeToString([]byte(str))
	fmt.Println("byteCode:", byteCode)
	fmt.Println("stringCode:", strCode)

}

```

上述代码输出：

```
byteCode: CrvZDM+3uOe4lXaX
stringCode: CsK7w5kMw4/Ct8K4w6fCuMKVdsKX
```

由此可以看出，关于 ASCII 控制字符的转码，不可以用 string 转 byte 到 `EncodeToString` 方法，如果需要一致输出，需要直接用 `[]byte`  接收值传入 `EncodeToString` 即可
