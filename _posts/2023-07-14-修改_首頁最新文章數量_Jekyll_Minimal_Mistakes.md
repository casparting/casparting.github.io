---
title: 修改 首頁最新文章數量 Jekyll Minimal Mistakes
excerpt: >
  使用GitHub Pages Jekyll 來建立自己的部落格，想要調整部落格首頁顯示的文章數量。
categories:
  - Blog
tags:
  - GitHub Pages
  - Jekyll
  - Minimal Mistakes
---
## 調整首頁顯示的文章數量

在你的跟目錄裡面的_config.yml檔案內

修改
```yaml
# Outputting
permalink: /:categories/:title/
paginate: 10 # amount of posts to show
paginate_path: /page:num/
timezone: Asia/Taipei
```
paginate為你想顯示的文章數量~

## 結語
讓寫部落格文章也變成是在寫code吧~