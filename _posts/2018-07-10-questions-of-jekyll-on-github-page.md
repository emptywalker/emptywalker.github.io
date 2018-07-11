---
layout: post
title: Questions of Jekyll on Github Page
date: 2018-07-10 15:32:24.000000000 +09:00
---

## 描述

将[Vno-Jekyll](https://github.com/onevcat/vno-jekyll)下载后，copy到`<username>.github.io`的仓库 `root` 目录下, 执行 `bundle install`安装好当前`Jekyll`的依赖。效果如下

```
EmptyWalker-2:github-site emptywalker$ bundle install
Fetching gem metadata from https://rubygems.org/...........
Fetching public_suffix 2.0.5
Installing public_suffix 2.0.5
Fetching addressable 2.5.0
Installing addressable 2.5.0
Using bundler 1.16.2
Using colorator 1.1.0
Fetching ffi 1.9.17
Installing ffi 1.9.17 with native extensions
Using forwardable-extended 2.6.0
Fetching sass 3.4.23
Installing sass 3.4.23
Fetching jekyll-sass-converter 1.5.0
Installing jekyll-sass-converter 1.5.0
Fetching rb-fsevent 0.9.8
Installing rb-fsevent 0.9.8
Fetching rb-inotify 0.9.8
Installing rb-inotify 0.9.8
Fetching listen 3.0.8
Installing listen 3.0.8
Fetching jekyll-watch 1.5.0
Installing jekyll-watch 1.5.0
Fetching kramdown 1.13.2
Installing kramdown 1.13.2
Fetching liquid 3.0.6
Installing liquid 3.0.6
Using mercenary 0.3.6
Fetching pathutil 0.14.0
Installing pathutil 0.14.0
Fetching rouge 1.11.1
Installing rouge 1.11.1
Using safe_yaml 1.0.4
Fetching jekyll 3.4.0
Installing jekyll 3.4.0
Fetching jekyll-paginate 1.1.0
Installing jekyll-paginate 1.1.0
Bundle complete! 2 Gemfile dependencies, 20 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```


依赖安装完毕后执行`jekyll serve`就可以开启本地调试了：

```
EmptyWalker-2:github-site emptywalker$ jekyll serve
Configuration file: /Users/emptywalker/Documents/EmptyGitHub/github-site/_config.yml
Configuration file: /Users/emptywalker/Documents/EmptyGitHub/github-site/_config.yml
            Source: /Users/emptywalker/Documents/EmptyGitHub/github-site
       Destination: /Users/emptywalker/Documents/EmptyGitHub/github-site/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 1.278 seconds.
 Auto-regeneration: enabled for '/Users/emptywalker/Documents/EmptyGitHub/github-site'
Configuration file: /Users/emptywalker/Documents/EmptyGitHub/github-site/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

至此，将当前的代码push到仓库里就可以使用`https://<username>.github.io`访问主页了

### 问题描述
如果我直接在`_post`文件夹下添加`YYYY-MM-DD-name.md`文档，是需要执行`jekyll serve`，先生成对应的html，然后再push到master分支后，才能生效，如何在`github page`中自动生效



---

### `GitHub Pages is temporarily down for maintenance.`
在访问`GitHub Pages`上部署的网站时，出现暂时维护的状态，不清楚是配置有问题还是真的在维护，这个维护时间多久？都不确定

**答案：**整个过程大约持续了半小时，恢复访问


### Swift 中Dictionary 无法根据特定value获取对应的key

