---
title: 使用 hexo-git-backup 插件备份你的 Hexo 博客
categories: Hexo
tags:
 - Hexo
 - 备份
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/hexo.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
description: 一键备份博客数据，再也不怕数据丢失了！
---

欢迎关注我的 CSDN 专栏：[《个人博客搭建：Hexo+Github Pages》](https://blog.csdn.net/qq_36759224/article/category/9285510)，从搭建到美化一条龙，帮你解决 Hexo 常见问题！

---

由于 Hexo 博客是静态托管的，所有的原始数据都保存在本地，如果哪一天电脑坏了，或者是误删了本地数据，那就是叫天天不应叫地地不灵了，此时定时备份就显得比较重要了，常见的备份方法有：打包数据保存到U盘、云盘或者其他地方，但是早就有大神开发了备份插件：[hexo-git-backup](https://github.com/coneycode/hexo-git-backup) ，只需要一个命令就可以将所有数据包括主题文件备份到 github 了

首先进入你博客目录，输入命令 `hexo version` 查看 Hexo 版本，如图所示，我的版本是 3.7.1：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A53/01.png)
</fancybox>

安装备份插件，如果你的 Hexo 版本是 2.x.x，则使用以下命令安装：

```bash
$ npm install hexo-git-backup@0.0.91 --save
```

如果你的 Hexo 版本是 3.x.x，则使用以下命令安装：

```bash
$ npm install hexo-git-backup --save
```

到 Hexo 博客根目录的 `_config.yml` 配置文件里添加以下配置：

```bash
backup:
  type: git
  theme: material-x-1.2.1
  message: Back up my www.itrhx.com blog
  repository:
    github: git@github.com:TRHX/TRHX.github.io.git,backup
    coding: git@git.dev.tencent.com:TRHX/TRHX.git,backup
```

参数解释：

- theme：你要备份的主题名称
- message：自定义提交信息
- repository：仓库名，注意仓库地址后面要添加一个分支名，比如我就创建了一个 backup 分支

最后使用以下命令备份你的博客：

```bash
$ hexo backup
```

或者使用以下简写命令也可以：

```bash
$ hexo b
```

备份成功后可以在你的仓库分支下看到备份的原始文件：

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A53/02.png)
</fancybox>

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A53/03.png)
</fancybox>
