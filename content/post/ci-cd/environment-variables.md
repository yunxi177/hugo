+++
title = "jenkins pipeline 环境变量详解"
date = 2021-06-07T17:16:46+08:00
tags = ["jenkins", "CI/CD", "运维"]
categories = ["jenkins", "CI/CD","运维"]
draft = false
commentId="jenkins-environment-variables"

+++

关于 Jenkins 的环境变量，可以分为系统内置环境变量和自定义环境变量。系统内置环境变量是 Jenkins 内部定义的环境变量。自定义环境变量是用户自己定义的环境变量

# 系统环境变量

jenkins 的内置环境变量的查看方式有两种，一种是通过 web url 地址查看，另一种是通过 shell 命令 `printenv` 查看

## 方式一:通过地址访问

直接在浏览器中访问 `${YOUR_JENKINS_HOST}/env-vars.html` 页面就可以，比如 `http://localhost:8080/env-vars.html` ，每个变量的用途写的都很清楚

![image-20210607173228000](https://pic.artacode.com/image-20210607173228000.png)

## 方式二：printenv

通过 shell 命令` printenv ` 获取

```jenkins
pipeline {
    agent any

    stages {
        stage("Env Variables") {
            steps {
                sh "printenv"
            }
        }
    }
}
```

通过执行上述 `pipeline` 的构建，就可以打印出系统内置的环境变量

# 自定义环境变量

在内置环境变量的基础上，有事我们也需要定义我们自己的的环境变量来方便开发和部署，自定义环境变量的设置分为三种，分别是：声明式，脚本式，内置函数式，接下来分别讲解一下三种方式各是如何设置的。

## 声明式

声明式定义结构如下：

```
environment {
        key = value
}
```

声明式可以在 pipeline 的任意阶段声明，看如下示例

```jenkins
pipeline {
    agent {
        label any
    }
    environment { //全局环境变量
            NAME = "zhangsan"
    }
    stages {
        stage('Build') {
          environment { // 仅在 Build 阶段下有效的环境变量
                NAME = "Andy"
            }
            steps {
                sh 'printenv'
            }
        }
    }
}
```

## 脚本式

脚本式基本结构如下:

```
env.key = value
```

脚本式示例如下

```
pipeline {
    agent {
        label any
    }
    stages {
        stage('Build') {
            steps {
	            script {
                    env.NAME = "ZHANGSAN"
                }
                sh 'printenv'
            }
        }
    }
}
```

## 内置函数式

内置函数式基本结构如下:

```
withENv(["key=value"]) {

}
```

示例如下：

```
pipeline {
    agent {
        label any
    }
    stages {
        stage('Build') {
            steps {
	            withEnv(["NAME=zhangsan"]) {
	                sh 'printenv'
                }
            }
        }
    }
}
```

**注意：`withEnv(["NAME=zhangsan"]) {}` **这里的 = 号两侧不能有空格**，必须是 `key=value` 的形式**



# 重写环境变量

重写环境变量遵守以下原则：

1. `withEnv(["NAME=zhangsan"]) {}` 内置函数的这种写法，可以重写任意环境变量
2. 定义在 `environment {}` 的环境变量不能被脚本式定义的环境变量（`env.key="value"`）重写
3. 脚本式环境变量只能重写脚本式环境变量



重写示例如下：

```
pipeline {
    agent {
        label any
    }
    environment {
            NAME = "zhangsan"
    }
    stages {
        stage('Build') {
            steps {
	            withEnv(["NAME=Andy"]) {
	                sh 'printenv' // name=andy
                }
                script {
                    env.AGE = "18"
                }
                
                withEnv(["AGE=19"]) {
	                sh 'printenv' // AGE=19
                }
            }
        }
    }
}
```



# 使用 boolean 值

jenkins  环境变量默认式 string 类型，那么我们如何使用 boolean 类型的值呢，如何将字符串的 'false' 转换成 boolean 的 true 呢？
转 boolean 示例如下:

```
pipeline {
    agent any

    environment {
        IS_BOOLEAN = false
    }

    stages {
        stage("Env Variables") {
            steps {
                script {
                    if (env.IS_BOOLEAN) {
                        echo "Hello"
                    }

                      // 真正的 Boolean 比较
                    if (env.IS_BOOLEAN.toBoolean() == false) {
                        echo "日拱一兵"
                    }
                  
                      // 真正的 Boolean 
                    if (!env.IS_BOOLEAN.toBoolean()) {
                        echo "RGYB"
                    }
                }
            }
        }
    }
}
```



# Shell 结果赋值给环境变量

基本格式如下:

```
sh(script: 'cmd', returnStdout:true)
```

这里 returnStdout 会将使返回值里多一个空格，可以使用 `.trim()` 去除空格，

```
pipeline {
    agent any

    environment {
          // 使用 trim() 去掉结果中的空格
        LS_RESULT = "${sh(script:'ls -lah', returnStdout: true).trim()}"
    }

    stages {
        stage("Env Variables") {
            steps {
                echo "LS_RESULT = ${env.LS_RESULT}"
            }
        }
    }
}
```





