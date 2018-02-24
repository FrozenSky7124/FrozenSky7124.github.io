---
layout: post
title: "GithubPage + Jekyll 搭建博客空间"
categories: Linux
tags: GitHubPage Jekyll 环境搭建 Blog
author: FrozenSky
---

在上一篇Blog中，已经详细记录了如何在Ubuntu 16.04上搭建Ruby & Jekyll环境！现在就要开始用Jekyll搞些事情啦！（GitHubPage瑟瑟发抖...

#### 参考资料

简书上的GitHubPage攻略：https://www.jianshu.com/p/85ca31174488

本文所使用的Jekyll主题：https://github.com/Gaohaoyang/gaohaoyang.github.io

#### 获取Jekyll主题

- 这里采用了Gaohaoyang设计修改的主题，鸣谢作者！

> ~$ git clone https://github.com/Gaohaoyang/gaohaoyang.github.io.git

#### 启动Jekyll

- 新建一个站点目录或者直接用上面clone到的文件夹当作网站根目录，测试一下

> --新建站点目录
> 
> ~$ jekyll new GitHubPage_Root
> 
> --进入站点目录
>
> ~$ cd ./GitHubPage_Root
>
> --启动Jekyll服务
>
> ~$ jekyll serve

- 此时会报错，提示缺少插件：jekyll-paginate

> Deprecation: The 'gems' configuration option has been renamed to 'plugins'. Please update your config file accordingly.
>
> Dependency Error: Yikes! It looks like you don't have jekyll-paginate or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jekyll-paginate' If you run into trouble, you can find helpful resources at https://jekyllrb.com/help/! 
>
> jekyll 3.7.2 | Error:  **jekyll-paginate**

- 主题的说明中也提到了，因为新版Jekyll没有集成Paginate插件，所以需要手动安装插件到环境中

> ~$ gem install jekyll-paginate

- 再次启动Jekyll服务

> ~$ jekyll serve

- 如果出现像下面一样的提示就表示启动成功啦
 
> Configuration file: /home/frozensky/GitHubPage_Root/_config.yml
> 
> Deprecation: The 'gems' configuration option has been renamed to 'plugins'. Please update your config file accordingly.
>        
> Source: /home/frozensky/GitHubPage_Root
>             
> Destination: /home/frozensky/GitHubPage_Root/_site
>        
> Incremental build: disabled. Enable with --incremental
>  
> Generating... 
>  
> done in 1.325 seconds.
>  
> Auto-regeneration: enabled for '/home/frozensky/GitHubPage_Root'
>  
> Server address: http://127.0.0.1:4000/
>  
> Server running... press ctrl-c to stop.

- 在地址栏中输入http://127.0.0.1:4000就可以访问到本地的网站了

至此，本地调试用的Jekyll环境搭建完成。

#### GitHub Page

- 创建GitHub仓库

具体步骤见参考资料。

注意！GitHub仓库的名称必须为：[YourAccountName].github.io

- Clone远程库到本地

> ~$ git clone git@github.com:FrozenSky7124/FrozenSky7124.github.io.git

- 复制本地网站根目录的文件到Git本地库

- 创建.gitignore文件，并添加需要忽略的文件和目录

这里我们需要在该文件中添加"_site"，这个目录是本地Jekyll调试环境所生成的静态网页文件，不需要上传到GitHub仓库中。

- 关联本地库和远程库，并上传。

注意！首次操作可能提示缺少密钥，具体解决方法参考GitHub官方说明。

> ~$ git remote add origin git@github.com:FrozenSky7124/FrozenSky7124.github.io.git

> ~$ git add .

> ~$ git commit -m "XXXXXXXX"

稍等片刻，GitHubPage服务器就会将我们上传的文件进行转换，变成静态网页。

通过[YourAccountName].github.io 就可以正常访问了。
