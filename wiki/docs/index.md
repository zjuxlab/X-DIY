# EE手册

## 前言

本书是一个硬件DIY的自学手册，旨在让更多人能够**有迹可循**地自学EE知识和掌握硬件产品DIY的技能。

我在高中时就看过很多科幻作品和技术视频，也常常幻想着能像蜘蛛侠、钢铁侠那样，用技术做出酷炫的装备。但是在那时，不像一些身边有资源有门道的朋友，我完全不知道如何入门，不知道怎么去网上寻找资源，不知道学习的路径，所以只能眼巴巴望着羡慕。直到上了大学，选了一门工科专业，进入了一个泛技术学生组织(X-Lab)，我才开始迈入硬件DIY的大门。

我很清楚的知道，一定有很多人像高中时候的我一样，对技术渴望，充满好奇。所以我们写下了这本书，希望有更多对DIY，对技术有热情的朋友们

### 本书结构

硬件是个太宽泛的定义，实际上任何技术都是多少和硬件相关的（哪怕是纯算法工程师，也要考虑硬件架构才能设计出最优算法）。因此在这个手册中，我们会关注硬件DIY中会用到的一些技术栈，包括但不限于「C语言、电路设计、嵌入式开发、3D建模」。

目前手册分为以下几个板块：

- 编程基础
- 电路设计
- 嵌入式开发
- 数理基础
- 工具
- ...

（手册会一直更新不断同步）

## 加入X-DIY

如果你也想加入X-DIY贡献者的行列，分享自己的DIY项目经验或者写一些技术文档，可以直接在项目中发起 Pull Request。以下是向X-DIY添加内容的详细教学：

### 1. 将本仓库Fork到你的账户中：

![fork1](./images/readme/fork1.png)

![fork2](./images/readme/fork2.png)

### 2. 将你账户中的仓库Clone到本地

```sh
# 格式：
git clone https://github.com/[你的账户名]/[Fork的仓库名].git
#例如：
git clone https://github.com/tsaoo/X-DIY.git
```

### 3. 做出更改，Commit到你自己的仓库

文档/wiki/docs中。作为例子，我在/wiki/docs/工具/优质资源合集中添加了一个点号。现在，保存这一更改，将其提交并上传到云端：

```sh
cd D:\Projects\Xdiy\X-DIY           #转到工程根文件夹
git add .                           #将所有更改添加到缓存
git status                          #检查你的更改
git commit -m 'Add an extra dot'    #告诉大家你干了啥
git push origin main                #将你的修改提交到云端的main分支
```

### 4. 提交Pull Request

在你自己的仓库中，点击Contribute->Open pull request
![request1](./images/readme/request1.png)

![request2](./images/readme/request2.png)

往下翻一点，你可以在提交前检查更改的内容。
![request3](./images/readme/request3.png)

### 5. 等待管理员手动将Pull request整合到主页面中

注意：你可以自己建新页面，也可以修改已经存在的页面，或者将某个页面移动到其它分类下——你想怎么改就怎么改，管理员会**人肉**处理任何可能的冲突。
