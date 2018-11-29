+++
title = "swoft单元测试"
date = 2018-08-05T11:08:39+08:00
tags = ["swoft"]
categories = ["swoft"]
commentId="swoftUnit"
+++


## 1.1 为什么要使用单元测试
   在编写代码的过程中，一定会反复调试保证它能够编译通过。但代码通过编译，只是说明了它的语法正确。无法保证它的语义也一定正确，没有任何人可以轻易承诺这段代码的行为一定是正确的。幸运的是，单元测试会为我们的承诺做保证。编写单元测试就是用来验证这段代码的行为是否与我们期望的一致。有了单元测试，我们可以自信地交付自己的代码，减少后顾之忧。
   <!--more-->
   
进行单元测试，会带来以下好处:

* 软件质量最简单、最有效的保证；
* 是目标代码最清晰、最有效的文档；
* 可以优化目标代码的设计；
* 是代码重构的保障；
* 是回归测试和持续集成的基石。

## 1.2 swoft中使用单元测试
在swoft项目中是有单元测试代码的，这里讲述一下如何在swoft中讲单元测试跑起来，以及我在使用单元测试时遇到的问题。
### 1.2.1 安装环境
执行以下代码
```
➜ wget http://phar.phpunit.cn/phpunit.phar

➜ chmod +x phpunit.phar

➜ sudo mv phpunit.phar /usr/local/bin/phpunit

➜ phpunit --version
PHPUnit 6.3.0 by Sebastian Bergmann and contributors.
```

> `注意：如果你swoft是跑在docker里的，那么你上述代码的安装命令应该在你docker环境中执行`

### 1.2.2 phpunit简单使用
安装完环境，我们就可以在swoft正常实用单元测试了，在swoft跟目录下直接输入命令
```phpunit test/Cases/DemoControlle```
就可以执行对`DemoController`的单元测试了，当然你也可以对整个`/test/Cases`目录进行测试命令为
```phpunit test/Cases/```
对于phpunit一些配置行在`/swoft/phpunit.xml`中，如果需要增加配置可以直接修改`/swoft/phpunit.xml`
如果在非根目录下执行`phpunit`需要用`-c`加载配置文件才可以正常使用如：`phpunit -c phpunit.xml test/Cases/DemoControlle`

> `注意：在写单元测试的时候测试类必须继承\Swoft\Test\AbstractTestCase class`

## 1.3 结束
swoft实用单元测试到这就已经差不多了，最后我想说一点，单元测试在日常开发中是十分有必要的，这样会省去你很多的测试时间，测试代码一次书写，永久受益，当你重构代码时，只需要跑一遍单元测试就可以知道就可以知道重构代码是否存在问题，这样的好处诱惑是足够大的，哪怕在编写的时候需要废一点时间，也是值得的。