---
title: "Git提交规范"
date: 2020-04-22T17:54:52+08:00
draft: false
author: "WenpiCai"
tags: ["笔记"]
showFullContent: false
---

参考链接 https://juejin.im/post/5afc5242f265da0b7f44bee4

```
type: commit 的类型
feat: 新特性
fix: 修改问题
refactor: 代码重构
docs: 文档修改
style: 代码格式修改, 注意不是 css 修改
test: 测试用例修改
chore: 其他修改, 比如构建流程, 依赖管理.
scope: commit 影响的范围, 比如: route, component, utils, build...
subject: commit 的概述, 建议符合 [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)
body: commit 具体修改内容, 可以分为多行, 建议符合 [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)
footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.
```

