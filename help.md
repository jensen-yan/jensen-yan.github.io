## 网站说明

_config.yml 配置主题和插件

```bash
gem install bundler # 安装bundler管理Gemfile定义的依赖
gem install jekyll # 安装jekyll,把md文件转换成html
```

### 配置发布源
可以指定哪个分支或者哪个文件夹作为发布源，
例如/  或者 /docs

### 本地测试

```bash
bundle install # 安装依赖
bundle exec jekyll serve # 启动本地服务
# 访问 http://127.0.0.1:4000/
```

更新包
```bash
bundle update github-pages
```

### 添加内容
Jekyll 主要分为页面（page）和文章(post)两种内容，
页面是放在根目录下的md文件，例如about.md；
文章是放在_posts目录下的md文件，例如2023-07-13-algorithm.md。

#### 添加页面
在根目录下新建md文件，例如help.md，内容如下：
```markdown
---
layout: default
title: 博客帮助
permalink: /help/
---
帮助内容
```
那么会在网站上生成一个/help的页面，右上角访问

#### 添加文章
在_posts目录下新建md文件，例如2023-07-13-algorithm.md，内容如下：
```markdown
---
layout: default
title:  "算法套路总结"
date:   2023-07-13
---
内容
```
那么会在网站上生成一个文章

#### 转换cheetsheet

```
cheatsheet.sh cheatsheet.md
```

### jekyll 学习

默认生成的网页放在_site目录下，使用jekyll build命令生成网页，jekyll serve命令启动本地服务。
前面加上bundle exec 保证命令在特定的gem环境中执行！