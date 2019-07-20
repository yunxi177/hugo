+++
title = "exec.Command 返回 “exit status 1” 获取详细错误信息"
date = 2019-07-20T17:04:20+08:00
tags = ["golang"]
categories = ["golang"]
draft = true
commentId="golang-commands-exec-debug"
+++

```
cmd := exec.Command("find", "/", "-maxdepth", "1", "-exec", "wc", "-c", "{}", "\\")
output, err := cmd.CombinedOutput()
if err != nil {
    fmt.Println(fmt.Sprint(err) + ": " + string(output))
    return
}
fmt.Println(string(output))
```