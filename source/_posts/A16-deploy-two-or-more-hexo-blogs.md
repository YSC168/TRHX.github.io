---
title: 一台电脑使用两个/多个GitHub账号部署两个/多个Hexo博客
tags:
  - Hexo
  - Github
categories: Hexo
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/hexo.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
icons: [fas fa-heading]
---

![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A16/01.png)

由于个人原因需要在一台电脑上部署两个Hexo博客，本来以为挺简单，没想到问题重重，首先是一个GitHub账号只能搭建一个Hexo博客，因此就需要使用其他GitHub账号；其次是一台电脑绑定两个GitHub账号，则需要两对公钥，在处理第二个问题时遇到的问题比较多，因为对这方面一窍不通，还是小白，所以折腾了一下午才解决，网上好多教程我都看不懂，觉得不（自）够（己）详（太）细（笨），因此详细记录一下

<!-- more -->

原理分析：
> - SSH的公钥是GitHub作为本地仓库和远程仓库连接的唯一标识，一个公钥只能对应一个GitHub账户，如果将一个相同的公钥上传到不同的GitHub账户，GitHub则无法做出辨识，进而导致错误
> - 一台电脑，可以生成多对公私钥，可以通过配置，将不同的公钥上传到不同的GitHub账号，那么就不存在单个公钥绑定多个GitHub账号的情况存在了

相关问题报错：
> - 同一台电脑部署第二个Hexo博客执行`hexo g -d`时报错：`ERROR: Permission to xxxxxx/xxxxxx.github.io.git denied to xxxxxx.`
> - 添加新的 SSH 密钥 到 SSH agent 执行`ssh-add xxx`时报错：`Could not open a connection to your authentication agent.`
> - 单独设置用户名/邮箱时报错：`fatal: not in a git directory`


---

以下是详细过程：
前提：假设你的第二个博客相关配置操作已经顺利完成，但使用`hexo g -d`命令部署到 GitHub 上时报错：`ERROR: Permission to xxxxxx/xxxxxx.github.io.git denied to xxxxxx.`
# - 查看当前密钥
首先我们打开终端输入`ls ~/.ssh/`可以查看当前已有的密钥，显示`id_rsa` 与 `id_rsa_pub`说明已经有一对密钥
# - 创建新的密钥
首先使用以下命令进入 SSH根目录下：
```jboss-cli
cd ~/.ssh/
```
## 方法一
直接使用以下命令创建新密钥，然后两次回车即可：
```bash
ssh-keygen -t rsa -f  ~/.ssh/这里是新密钥名称 -C "这里是你的邮箱"
```
注意区别新密钥名称和旧密钥名称，不要相同！！！
## 方法二
使用下面命令行创建新密钥：
```mathematica
ssh-keygen -t rsa -C "这里是你的邮箱"
```
回车后会出现：
```vbnet
 Generating public/private rsa key pair.  
 Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): 
```
注意此时需要你输入新密钥的名称，同样要注意区别新密钥名称和旧密钥名称，不要相同！！！之后再两次回车，新密钥创建完毕！
# - 配置config
查看你的.ssh/根路径下, 有没有config文件,（ 比如我的路径为C:\Users\Lenovo\.ssh）没有则使用以下命令创建一个config文件：
```lsl
touch config
```
用记事本或者其他工具打开config文件（注意config文件是没有任何后缀名的），写入以下配置：

```crmsh
#第一个账号，默认使用的账号，不用做任何更改
Host github.com
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa
	
#第二个新账号，#"xxxxxx"为前缀名，可以任意设置，要记住，后面需要用到
Host xxxxxx.github.com
	HostName github.com
	User git
	IdentityFile ~/.ssh/这里是你创建的新密钥的名称
```
# - 设置新GitHub账户SSH key
输入以下命令复制你创建的公钥：
```jboss-cli
clip < ~/.ssh/这里是你创建的新密钥的名称.pub
```
也可以直接在.ssh目录下找到你创建的新的公钥，文件名为`新密钥的名称.pub`，（比如我的是`trhx_rsa.pub`），用记事本打开，复制里面的内容，然后打开你的新GitHub账号主页，依次进入Settings —> SSH and GPG keys —> New SSH key，将刚复制的内容粘贴到Key那里，Title可以随便填，点击Add Key保存。

# - 清空本地的 SSH 缓存，添加新的 SSH 密钥 到 SSH agent中
使用命令`cd ~/.ssh`cd到.ssh根目录下，依次执行以下命令：

```dockerfile
ssh-add -D
ssh-add xxxxxx #旧密钥名称，一般是id_rsa
ssh-add xxxxxx #新创建的密钥名称
```
如果执行以上命令出现错误：`Could not open a connection to your authentication agent.`，那么就需要先执行`ssh-agent bash`，再执行以上命令

# - 验证配置是否成功
依次执行以下命令，第一个为默认ssh_key验证；第二个为新的ssh_key验证，其中“xxxxxx”为你先前在config文件中的命名
```stylus
ssh -T git@github.com
ssh -T git@xxxxxxx.github.com
```
依次显示以下信息, 则说明配置成功：
```ada
Hi 你的用户名! You've successfully authenticated, but GitHub does not provide shell access.
```

# - 取消全局用户名/邮箱配置，单独设置用户名/邮箱
执行如下命令，取消全局用户名和邮箱配置（如果已经设置了全局的话）：

```dsconfig
git config --global --unset user.name
git config --global --unset user.email
```
分别进入你的两个Hexo博客.git目录下执行以下命令单独设置用户名/邮箱：
```stylus
git config user.name "这里是用户名"
git config user.email "这里是你的邮箱"
```
如果此时报错：`fatal: not in a git directory`，说明你没有进入.git目录下，具体路径：\Hexo\\.deploy_git\\.git，.git目录是隐藏的，需要你设置隐藏目录可见

执行以下命令可以查看设置是否成功
```lua
git config --list
```

# - hexo 配置文件修改git地址
打开你的第二个博客Hexo目录下的_config.yml文件，找到deploy关键字，写入以下配置并保存：
```less
deploy:
  type: git
  repository: git@xxxxxx.github.com:你的用户名/你的用户名.github.io.git
  branch: master
```
比如我的配置：
```less
deploy:
  type: git
  repository: git@love109.github.com:love109/love109.github.io.git
  branch: master
  ```
大功告成，再次执行hexo g -d就能成功将新的博客部署到 Github 上了