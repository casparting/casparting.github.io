---
title: 建立自己的部落格 Vscode + Jekyll + GitHub Pages + Docker Alpine + Minimal Mistakes
excerpt: >
  使用GitHub Pages來建立自己的部落格，並且使用Docker來Demo網頁，主題使用Minimal Mistakes.
categories:
  - Blog
tags:
  - GitHub Pages
  - Jekyll
  - Minimal Mistakes
  - Docker
  - Alpine
  - Vscode
---
## 前言
原本就熱愛分享知識的我，還是想要寫一些技術文章來分享，畢竟google受益良多，也想回饋給需要的朋友，舊的blog是用google內建的，相對於GitHub Pages，GitHub Pages有著高度的客製化，且因為寫部落格就像在寫程式一樣(Code Your Blog Website)，所以也具有挑戰性，另外還有就是我喜歡它的主題看起來簡單乾淨。
## 我是參考下面網址下去改的
[New video: Develop Jekyll or GitHub Pages using Docker containers](https://talk.jekyllrb.com/t/new-video-develop-jekyll-or-github-pages-using-docker-containers/7199)

[How to use Develop Jekyll (and GitHub Pages) locally using Docker containers](https://github.com/BillRaymond/my-jekyll-docker-website#step-1-create-a-github-pages-repo)
## 我修改的部分
因為該網址的資料有點舊了，所以他的dockerfile需要修正，為了解決套件相依性問題。
問題內容如下：
[https://stackoverflow.com/questions/75452016/installation-messed-up-with-ruby-unable-to-install-jekyll](https://stackoverflow.com/questions/75452016/installation-messed-up-with-ruby-unable-to-install-jekyll)

因為出現底下Error
```
ERROR:  Error installing jekyll:
    The last version of sass-embedded (~> 1.54) to support your Ruby & RubyGems was 1.58.0. Try installing it with `gem install sass-embedded -v 1.58.0` and then running the current command again
    sass-embedded requires RubyGems version >= 3.3.22. The current RubyGems version is 3.1.2. Try 'gem update --system' to update RubyGems itself.
```
從別人的解答中得知可能是ruby版本過舊，所以我修改dockerfile的ruby版本

修改如下：
```
# Create a Jekyll container from a Ruby Alpine image

# At a minimum, use Ruby 2.5 or later
# Uncomment this line if you want to use GitHub Pages (Jekyll 3.9.x):
# FROM ruby:2.7-alpine3.15
FROM ruby:3.0.6-alpine3.16
# Uncomment this line if you want to use the latest version of Jekyll:
# FROM ruby:3.0.3-alpine3.15

# Add Jekyll dependencies to Alpine
RUN apk update
RUN apk add --no-cache build-base gcc cmake git

# Update the Ruby bundler and install Jekyll
RUN gem update bundler && gem install bundler jekyll
RUN gem install jekyll-include-cache jekyll-feed
RUN gem install jekyll-gist jekyll-sitemap jekyll-paginate

# Add Jekyll dependencies to Alpine
## RUN apk update
## RUN apk add --no-cache build-base gcc cmake git

# Update the Ruby bundler and install Jekyll
## RUN gem update bundler && gem install bundler jekyll -v 3.9.3
```
詳細差異可以看我的git diff
[https://github.com/casparting/casparting.github.io/commit/5f6cab4705f4118126f268098f4ac0d795faadd5](https://github.com/casparting/casparting.github.io/commit/5f6cab4705f4118126f268098f4ac0d795faadd5)

底下這網址裡面可以看有哪些alpine的版本

[docker for ruby](https://hub.docker.com/_/ruby)
## 網頁Demo測試
當環境建置好docker image build好之後，我們透過vscode進入到container，並執行底下指令來快速demo我們的網頁。為什麼要這樣Demo不直接git push上去，因為git push上去之後GitHub Pages需要時間編譯，不會馬上更新到你的blog。
```
$ bundle exec jekyll serve
```
ref: [https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
## 結語
讓寫部落格文章也變成是在寫code吧~