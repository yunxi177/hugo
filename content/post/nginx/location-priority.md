+++
title = "nginx location 优先级"
date = 2021-05-19T13:52:44+08:00
tags = ["nginx"]
categories = ["nginx"]
draft = false
commentId="nginx-location-priority"

+++

#  location 优先级介绍

location 匹配方式有以下几种

1. `location = /path/a/exact.png {}`: 精确匹配

2. `location  ^~  /path/a/ {}`: 优先前缀匹配（符合最长匹配原则）

3. `location ~ /Path?/ {} ` ： 区分大小写正则匹配（首次匹配原则）

4. `location ~* /path?/ {} `: 不区分大小写正则匹配（首次匹配原则）

5. `location /path/a/test.png {}` :  前缀匹配（符合最长匹配原则）

location 优先级匹配与配置的先后顺序无关，优先级排列顺序如下

精确匹配 > 优先前缀匹配 > 区分大小写正则匹配=不区分大小写正则匹配 >  前缀匹配

# 实例说明

``` nginx
location = /path/a/exact.png {
    [ configuration A ]
}

location ~ /Path?/ {
    [ configuration B ]
}

location ~* /path?/ {
    [ configuration C ]
}

```

如果理解了优先级，将很容易得出如下结论:

 www.web.com/path/a/exact.png => 匹配 `configuration A`

 www.web.com/Path/a => 匹配 `configuration B`

 www.web.com/path/a => 匹配 `configuration C`

当我们发现能够很好的猜测各种 URL 所匹配各个 `location` 是否心中有些许窃喜，对 `location` 优先级的理解已经不再话下了？事实并非如此，关于 `location` 优先级，还有一件事你需要知道。

# One More Thing

接下来看下面例子

```nginx
location /path/a/test.png {
    [ configuration A ]
}

location ^~ /path/a/ {
    [ configuration B ]
}

location ~* /path?/ {
    [ configuration C ]
}
```



 www.web.com/path/a/test.png   => 匹配？

按照前文优先级排序  优先前缀匹配 > 正则匹配 > 前缀匹配，我们应该会猜测匹配 `configuration B`,但是事实匹配的确是 `configuration C`,为什么会这样呢？ 

我们可以看出上述例子中存在前缀匹配与优先前缀匹配，两者同时存在时会根据最长优先原则匹配出 `location /path/a/test.png`，然后在与 `location ~* /path?/` 对比优先级,按照前文规则，正则匹配 > 前缀匹配，故匹配 `configuration C `

# Summary

通过本文，你应该对 `location` 优先级有了一个比较全面的理解。如果在以后的 nginx 配置中发现 location 配置未命中，除了考虑配置文件找错之外，还需要从优先级的角度去考虑，是否上下文中有优先级更高的匹配被命中返回.

# References

[1.]: http://nginx.org/en/docs/http/ngx_http_core_module.html#location	"location"

