# EE手册

## 前言

本书是一个EE的自学手册，旨在让更多人能够**有迹可循**地自学EE知识和掌握硬件产品DIY的技能

## 注：如何向X-DIY添加内容

### 1. 将本仓库Fork到你的账户中：

![fork1](../../readme/fork1.png)

![fork2](../..//readme/fork2.png)

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
![request1](../../readme/request1.png)

![request2](../../readme/request2.png)

往下翻一点，你可以在提交前检查更改的内容。
![request3](../../readme/request3.png)

### 5. 等待管理员手动将Pull request整合到主页面中

注意：你可以自己建新页面，也可以修改已经存在的页面，或者将某个页面移动到其它分类下——你想怎么改就怎么改，管理员会**人肉**处理任何可能的冲突。
