+++
title = "nodejs常见问题"
date = 2018-09-15T12:44:30+08:00
tags = ["nodjs"]
categories = ["nodejs"]
draft = false
gitmentId="nodejs-faq"
+++

# 错误1
```
npm ERR! Linux 4.15.0-34-generic
npm ERR! argv "/usr/bin/node" "/usr/bin/npm" "install" "--no-bin-links"
npm ERR! node v8.10.0
npm ERR! npm  v3.5.2
npm ERR! code EMISSINGARG

npm ERR! typeerror Error: Missing required argument #1
npm ERR! typeerror     at andLogAndFinish (/usr/share/npm/lib/fetch-package-metadata.js:31:3)
npm ERR! typeerror     at fetchPackageMetadata (/usr/share/npm/lib/fetch-package-metadata.js:51:22)
npm ERR! typeerror     at resolveWithNewModule (/usr/share/npm/lib/install/deps.js:456:12)
npm ERR! typeerror     at /usr/share/npm/lib/install/deps.js:457:7
npm ERR! typeerror     at /usr/share/npm/node_modules/iferr/index.js:13:50
npm ERR! typeerror     at /usr/share/npm/lib/fetch-package-metadata.js:37:12
npm ERR! typeerror     at addRequestedAndFinish (/usr/share/npm/lib/fetch-package-metadata.js:82:5)
npm ERR! typeerror     at returnAndAddMetadata (/usr/share/npm/lib/fetch-package-metadata.js:117:7)
npm ERR! typeerror     at pickVersionFromRegistryDocument (/usr/share/npm/lib/fetch-package-metadata.js:134:20)
npm ERR! typeerror     at /usr/share/npm/node_modules/iferr/index.js:13:50
npm ERR! typeerror This is an error with npm itself. Please report this error at:
npm ERR! typeerror     <http://github.com/npm/npm/issues>
npm ERR! Linux 4.15.0-34-generic
npm ERR! argv "/usr/bin/node" "/usr/bin/npm" "install" "--no-bin-links"
npm ERR! node v8.10.0
npm ERR! npm  v3.5.2
npm ERR! code EMISSINGARG


```

## 解决方法
`npm install -g npm@latest`
参考链接：https://github.com/npm/npm/issues/7308