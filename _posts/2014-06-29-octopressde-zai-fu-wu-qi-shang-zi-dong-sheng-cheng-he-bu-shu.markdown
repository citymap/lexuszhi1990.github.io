---
layout: post
title: "Octopress的在服务器上自动生成和部署"
date: 2014-06-29 15:36:52 +0800
comments: true
categories: [tech, octopress, deploy, git]
published: true
---

我的blog开始是用github page做的，但是天朝网络无规则抽风，访问太慢，经常10s无反应。所以觉得还是把blog部署到我的vps上面去。

考虑再三之后决定按照[这篇文章的方法](http://www.xiaozhou.net/octopress-auto-generate-and-deploy-2013-08-15.html)，让blog的生产和部署都再服务器上面自动完成。也就是说，我们只要把blog写好psuh到server上面，服务端上的Git Hooks脚本，在检测到客户端push过来的改动后，自动生成静态blog页面文件，并部署到Nginx配置的blog目录中。

### 服务器上面的配置

<!-- more -->

首先登入vps 服务器，然后执行

```
bash | curl https://gist.githubusercontent.com/lexuszhi1990/e572a2614b7d610295ad/raw/077b0abdf6479d06bfa3ecabb846f3d9e57e33a6/octopress_setup.sh
```

然后回到的本地，将vps的裸创库加到本地的remote list当中

```
git remote add vps username@myvps.com:/home/deploy/blog.git
```

然后push的时候，就会触发post-receice这个hook。它会做如下的事情，

```
remote: Your bundle is complete!
remote: Use `bundle show [gemname]` to see where a bundled gem is installed.
remote: ## Generating Site with Jekyll
remote: unchanged sass/screen.scss
remote: Configuration from /home/deploy/DAVID_BLOG/work-dir/_config.yml
remote: Building site: source -> public
remote: Successfully generated site: source -> public
To deploy@162.243.130.113:/home/deploy/DAVID_BLOG/blog.git
   f8648aa..4fe5f78  source -> source
```
也就是 bundle install，然后 generate 新的页面，然后将他们cp到ocpress目录下去。
关于git的hook具体就是参考[这篇文章](http://gitbook.liuhui998.com/5_8.html)

nginx的配置就是如下

```
server {
        listen 80;

        root /home/deploy/DAVID_BLOG/octopress;
        index index.html index.htm;

        location ~ ^/(assets)/  {
          root /path/to/public;
          gzip_static on; # to serve pre-gzipped version
          expires max;
          add_header Cache-Control public;
        }
        server_name blog.lingzhi.me www.blog.lingzhi.me;
}
```

