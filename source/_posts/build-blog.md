---
title: build-blog
date: 2020-02-25 13:37:49
tags: 
  - blog 
categories:
  - 其他
---

### 前期准备

#### 准备环境

  - [Node.js](https://nodejs.org/en/) 下载，并安装
  - [Git](https://git-scm.com/) 下载，并安装
  - 安装Hexo 

  ``` bash
    npm install -g hexo-cli
  ```
  - 初始化Hexo
  ``` bash
    hexo init <folder>
    cd <folder>
    npm install
  ```
  - 启动服务器
  ``` bash
    hexo server
  ```
  - 浏览器访问网址 <code>http://localhost:4000</code>

#### 实施方案
  1. 云服务+域名

  购买云服务和域名

    + 1-1 在云服务器安装Git 和 Nginx。(Git 用于版本管理和部署，Nginx 用于静态博客托管。)

      登陆root用户，运行
      ```bash
        yum -y update
        yum install -y git nginx
      ```

    + 1-2 Nginx 配置

      * 创建Nginx配置
      ``` bash
        cd /usr/local/
        mkdir hexo
        chmod 775 -R /usr/local/hexo/
      ```
      * 添加index.html
      ``` bash
        vim /usr/local/hexo/index.html
      ```
      添加以下代码并保存
      ``` html
        <!DOCTYPE html>
        <html>
          <head>
            <title></title>
            <meta charset="UTF-8">
          </head>
          <body>
            <p>Nginx running</p>
          </body>
        </html>
      ```
      * 配置nginx
      ``` bash
        vim /etc/nginx/nginx.conf
      ```
      修改server_name和root
      ``` bash
        server {
          listen       80 default_server;
          listen       [::]:80 default_server;
          server_name  www.yoursite.com; # 填个人域名      
          root         /usr/local/hexo/;
        }      
      ```
      * 启动nginx
      ``` bash
        service nginx start
      ```

    + 1-3 git配置
      * 创建文件目录, 用于私人 Git 仓库搭建, 并更改目录读写权限
      ``` bash
        cd /usr/local/
        mkdir hexoRepo
        chmod 775 -R /usr/local/hexoRepo/
      ```
      * git初始化裸库
      ``` bash
        git init -bare hexo.git
      ```
      * 创建Git钩子
      ``` bash
        vim /usr/local/hexoRepo/hexo.git/hooks/post-receive
      ```
      * 指定Git源代码和Git配置文件
      ``` bash
        #!/bin/bash

        git --work-tree=/usr/local/hexo --git-dir=/usr/local/hexoRepo/hexo.git checkout -f
      ``` 
      * 保存退出，给该文件添加可执行权限
      ``` bash
        chmod +x /usr/local/hexoRepo/hexo.git/hooks/post-receive
      ```
    
    + 1-4 本地博客推送到云服务
      * 安装hexo-deployer-git插件
      ``` bash
        npm install hexo-deployer-git --save
      ```
      * 修改_config.yml
      ``` bash
        depoly:
          type: git
          repo: root@url:/usr/local/hexoRepo/hexo
          branch: master
      ```
      * 推送到服务器
      ``` bash
        hexo g
        hexo d
      ```
      * 访问个人域名即可访问博客

#### 主题优化

  参考 [hexo-theme-pure](https://github.com/cofess/hexo-theme-pure/blob/master/README.cn.md)