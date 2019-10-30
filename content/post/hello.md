---
title: "搭建博客"
date: 2019-10-29T23:56:28+08:00
draft: false
author: "WenpiCai"
cover: "https://gohugo.io/images/gohugoio-card.png"
tags: ["日常"]
description: "Hugo 搭建博客"
showFullContent: false
---

# Hello world!

## 步骤操作

参考 https://mogeko.me/2018/018/

## 配置文件

```toml
baseurl = "https://caiwp.github.io"
languageCode = "zh-cn"
theme = "terminal" # https://github.com/panr/hugo-theme-terminal/
paginate = 10
preserveTaxonomyNames = true
publishDir = "../caiwp.github.io"
[Permalinks]
 posts =  "/:year/:filename/"

[params]
  contentTypeName = "post"
  # ["orange", "blue", "red", "green", "pink"]
  themeColor = "orange"
  showMenuItems = 1
  showLanguageSelector = false
  fullWidthTheme = false
  centerTheme = true

[languages]
  [languages.en]
    languageName = "English"
    title = "Terminal"
    subtitle = "My blog"
    keywords = ""
    copyright = ""
    menuMore = "Show more"
    readMore = "Read more"
    readOtherPosts = "Read other posts"

    [languages.en.params.logo]
      logoText = "Terminal"
      logoHomeLink = "/"

    [languages.en.menu]
      [[languages.en.menu.main]]
        identifier = "about"
        name = "About"
        url = "/about"

```

