---
layout: post
title: "为 git bash 设置代理"
---


<br>

由于直连到 `github` 的 `clone`, `pull`, `push` 等操作都很慢，我们需要为其设置代理，以加快连接 `github.com` 的速度

本文操作环境是 `Windows`

<br>

##	HTTPS

目前来看，设置代理后加速最明显的是 `HTTPS`， 为其设置代理的方式如下

打开 `git bash`，然后输入

```
git config --global http.proxy "http://127.0.0.1:1080"
git config --global https.proxy "https://127.0.0.1:1080"
```

这样设置之后，`git clone https://github.com/username/repo.git` 的速度基本能跑满带宽

但是，这种方式并不适用于 `git clone git@github.com:username/repo.git` (ssh方式通信)

<br>

##	SSH

目前来看，`https` 方式速度较快，但是它有一个缺点，就是 `git push` 的时候需要输入账号密码。因此我们最好也设置一下 `ssh` 通道的代理，说不定某些区域速度会比较快

打开 `git bash` 所在目录的 `.\etc\ssh`，新建文件 `config`，注意没有后缀名。添加以下内容

```
Host github.com
   User git
   IdentityFile "C:\Users\your-username\.ssh\id_rsa"
   ProxyCommand connect.exe -H 127.0.0.1:1080 %h %p
```

其中有一个代理连接软件 `connect.exe` 可以从[这里](https://stackoverflow.com/questions/5103083/ssh-in-git-behind-proxy-on-windows-7)寻找下载地址。

<br>

##	其他

除了设置代理，我们还可以设置 `hosts`

```
192.30.253.112	github.com
192.30.253.113	github.com
151.101.185.194   github.global.ssl.fastly.net
```

为了找到这俩域名的 `IP`，我们可以访问 `https://ipaddress.com/`

<br>

##	参考资料

*	https://imciel.com/2016/06/28/git-proxy/

*	https://stackoverflow.com/questions/5103083/ssh-in-git-behind-proxy-on-windows-7

*	https://help.github.com/en/articles/using-ssh-over-the-https-port

*	https://www.liaoxuefeng.com/wiki/896043488029600/898732792973664

<br><br>

本文地址：[https://jjayyyyyyy.github.io/2019/08/11/git_bash_proxy.html](https://jjayyyyyyy.github.io/2019/08/11/git_bash_proxy.html)

(END)

---

##	相关阅读
