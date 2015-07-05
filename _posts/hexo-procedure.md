title: "hexo博客建立全过程"
date: 2015-05-15 21:19:26
categories: 学习笔记
tags: [hexo,github,nodejs]
---

# 前言 
忘记了具体是怎么接触到nodejs的，只是感觉通过nodejs真切感觉到了，以后前端开发将会是趋势，所以也想着加入到前端开发的行列中来，在网上查询nodejs的学习路径，看见了使用Hexo搭建个人博客这么一条，于是便学着搭建起来一个，顺便以此作为学习的起点，网上的教程博客很多，一下的只是我个人操作的时候使用到 地方，趁着刚刚做完，还记得不少，就赶紧记录了下来。
顺便一提，由于nodejs的开发环境推荐使用Linux或者Mac OS X下进行，同时本人穷小子一个，所以就毫不犹豫的自己装了个虚拟机，在linux上进行开发了，所以一下所处环境都是在虚拟机里的ubuntu下进行的

<!--more-->

# github

github相信即使没用过，大家也都听过，目前非常多且著名的开源项目都托管在这里，而且由于其免费提供300M空间，目前在天朝也没有被墙，也能顺便学习一下git，所以想要找到一个免费的静态博客托管服务器，github毫无疑问是最佳选择之一。

## 配置和使用 github


首先，注册一个[github][1]帐号
### 配置SSH keys
我们如何让本地git项目与远程的github建立联系呢？用SSH keys。

>cd ~/. ssh   检查本机的ssh密钥
>ssh-keygen -t rsa -C "邮件地址@youremail.com" 生成新密钥
>Generating public/private rsa key pair.
>Enter file in which to save the key >(/Users/your_user_directory/.ssh/id_rsa):<回车就好>

>注意1: 此处的邮箱地址，你可以输入自己的邮箱地址；
>注意2: 此处的「-C」的是大写的「C」

然后系统会要你输入密码：

>Enter passphrase (empty for no passphrase):<输入加密串>
>Enter same passphrase again:<再次输入加密串>

如果出现下面的画面，就是配置成功了
![密钥生成成功](http://pic.yupoo.com/vankos_v/DKi6S7PO/lpjsl.png)
### 添加SSH Key到GitHub

 - 打开本地~/.ssh/id_rsa.pub文件。此文件里面内容为刚才生成人密钥。准确的复制这个文件的内容，才能保证设置的成功。
 - 登陆github系统。点击右上角的 Account Settings—->SSH Public keys —-> add another public keys
 - 把你本地生成的密钥复制到里面（key文本框中）， 点击 add key 就ok了
 
可以输入下面的命令，看看设置是否成功，git@github.com的部分不要修改：

>$ ssh -T git@github.com

如果是下面的反馈：

>The authenticity of host 'github.com (207.97.227.239)' can't be >established.
>RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
>Are you sure you want to continue connecting (yes/no)?

不要紧张，输入yes就好，然后会看到：

>Hi cnfeat! You've successfully authenticated, but GitHub does not >provide shell access.


最后


>git config --global user.name "cnfeat"//用户名
>git config --global user.email  "cnfeat@gmail.com"//填写自己的邮箱

至此，与github的链接建立完成

### 使用GitHub Pages建立博客
与GitHub建立好链接之后，就可以方便的使用它提供的Pages服务，GitHub Pages分两种，一种是你的GitHub用户名建立的username.github.io这样的用户&组织页（站），另一种是依附项目的pages。

想建立个人博客是用的第一种，形如cnfeat.github.io这样的可访问的站，每个用户名下面只能建立一个。
具体流程可以参考[一步步在GitHub上创建博客主页(2)][2]

# nodejs
目前国内比较活跃的是[Cnode社区][3]，有兴趣的可以去转转。
我是通过nvm安装node的
>cd ~/git
>git clone https://github.com/cnpm/nvm.git
>source ~/git/nvm/nvm.sh

然后就可以安装任意版本的nodejs了
>nvm install 0.12.0

具体可参考社区里新手教学的[快速搭建 Node.js / io.js 开发环境以及加速 npm][4]


# hexo
Hexo的作者是[tommy351][5]，根据[官方介绍][6]，Hexo是一个简单、快速、强大的博客发布工具，支持Markdown格式。

## 安装hexo
找到一个路径，新建一个文件夹，比如我的就叫做Hexo。进入此路径后
>npm install -g hexo
>hexo init

成功后此路径下就会多出一个完整的hexo项目。
>hexo g
>hexo s

浏览器输入http://localhost:4000，查看搭建效果。

PS：这个安装方法我使用后只能在这个Hexo目录下有效，这么设置成全局的我没有研究
常用命令如下：
>hexo new "postName" #新建文章
>hexo new page "pageName" #新建页面
>hexo generate #生成静态页面至public目录
>hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
>hexo deploy #将.deploy目录部署到GitHub

也可以简写：
>hexo n == hexo new
>hexo g == hexo generate
>hexo s == hexo server
>hexo d == hexo deploy

##克隆主题
成功以后，你会发现在你的目录下有个themes的文件夹，这个就是hexo的主题，你可以通过修改配置文件_config.yml中的theme的值来确定使用哪个主题。主题的下载地址是http://hexo.io/themes/,当然也有很多不错的主题并没有在这个页面里，需要你去一下论坛或什么地方找了，比如我现在使用的主题是https://github.com/litten/hexo-theme-yilia

具体使用起来很简单
> git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

然后你的themes文件夹下就多了个yilia的文件夹，修改_config.yml中的theme的就可以选定你要用的主题了，你可以多下载几个都试试效果，然后再确定最后使用谁的。
最后推荐一个学习hexo具体配置的地方
http://zipperary.com/categories/hexo/


  [1]: https://github.com/
  [2]: http://www.pchou.info/web-build/2013/01/05/build-github-blog-page-02.html
  [3]: https://cnodejs.org/
  [4]: http://fengmk2.com/blog/2014/03/node-env-and-faster-npm.html
  [5]: https://github.com/tommy351
  [6]: http://hexo.io/docs/index.html
