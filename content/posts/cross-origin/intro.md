+++
title = "跨域详解"
date = 2019-07-27T21:31:53+08:00
tags = ["后端开发"]
categories = ["后端开发"]
draft = true
commentId="cross-origin-intro"
+++

# 什么是跨域

|当前页面url|被请求页面url|是否跨域|原因
|-|-|-|-|
|http://www.test.com/|http://www.test.com/index.html|否|同源（协议、域名、端口号相同）
|http://www.test.com/|https://www.test.com/index.html|跨域|协议不同（http/https）
|http://www.test.com/|http://www.google.com/|跨域|主域名不同（test/google）
|http://www.test.com/|http://blog.test.com/|跨域|子域名不同（www/blog）
|http://www.test.com:8080/|http://www.test.com:7001/|跨域|端口号不同（8080/7001）

#为什么会出现跨域问题
出于浏览器的同源策略限制。[同源策略（Sameoriginpolicy）](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）

# 非同源限制
1. 无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB
2. 无法接触非同源网页的 DOM
3. 无法向非同源地址发送 AJAX 请求
