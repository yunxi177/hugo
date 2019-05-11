+++
title = "vue jsonp 跨域调用 腾讯地图 api报错"
date = 2019-05-11T07:12:56+08:00
tags = ["frontend"]
categories = ["frontend"]
draft = false
commentId="cross-origin-jsonp"
+++

今天帮助前端小伙伴解决了一下 jsonp 调用腾讯地图 WebService API 报错 `Unexpected token :` 的问题，这里记录一下解决过程

项目中使用 `vue-jsonp` 组件来解决跨域问题，使用流程如下

# 安装 jsonp

```
npm install vue-jsonp
```



# main.js 引入 jsonp 组件

```vue
import Vue from 'vue'
import VueJsonp from 'vue-jsonp'
Vue.use(VueJsonp)
```



# 接口调用

```
            const KEY =XXXXXXXXXXXXXX; //key 秘钥自己申请
	        let url = 'https://apis.map.qq.com/ws/geocoder/v1?&poi_options=address_format=short&get_poi=0';
	        let locationdata = lat+','+lng //纬度，经度
	        this.$jsonp(url,{
	          key:KEY,
	          location:locationdata
	        })
			.then(json => {
				let address = json.result.address;
	            console.log(address) //附近街道地址信息
					})
				.catch(err => {
						console.log(err)
				})
	      }
```

本来到了这里应该是完美无缺，开开心心的接受腾讯地图返回的信息就可以了，但是！！，出来了莫名其妙的问题，当请求接口是返回的确是 `Unexpected token :` 这样的错误。

# 问题排查

本着发现问题解决问题的程序员精神，开始了百度 ，谷歌的混合双打，依然没有找到问题的原因，只因为当时搜索的关键词为 `Unexpected token :`  虽然我本身不是做前端，但是我也清除这种错误一抓一大把，很难精准的把握到错误的具体位置，既然问题出现在了 `jsonp` 中那就了解一下 `jsonp` 的实现原理，和跨域原理，果然有重大发现下图中的一段话一句惊醒梦中人

![](http://pic.artacode.com/企业微信截图_20190511145411.png)

既然 `jsonp` 是要配合接口域名实现的服务端做配合那么，是不是腾讯那边也需要我来告诉它一下，我是通过 `jsonp` 调用的呢？于是果断查了一下接口文档，然后结果不出所料，接口中需要加入 `output` 参数来告诉腾讯地图访问请求为 `jsonp` 具体文档如下图

![](http://pic.artacode.com/企业微信截图_20190511145801.png)

当我告诉前端小伙伴传入此参数时，果然接口正常访问了，到此问题解决。



#  summary

出现这种问题最主要的问题是没有仔细的阅读文档，只关注了自己需要传值部分，忽略了一些参数，有些还是比较重要的参数（像这次）。   

其次就是我本身基础没有足够的扎实，对 `jsonp` 实现原理不熟悉，导致问题出现了，没有通过代码检查解决问题，而是通过百度和谷歌解决问题。  

此次时间我个人也是一个惊醒，首先就是一定要仔细看接口文档，其二就是对知识要不求甚解，以前对 `jsonp` 了解太少，不够深刻，导致问题出现并不能精准定位。