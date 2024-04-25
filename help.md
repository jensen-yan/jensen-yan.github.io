
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