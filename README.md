# X-DIY
X·Lab EE自学手册项目，基于mkdocs+GitHub WebHooks，实现了自动同步git push自动更新网页内容。

- [X-DIY](#x-diy)
  - [分支 | Branches](#分支--branches)
  - [架构说明 | Tree](#架构说明--tree)
  - [使用方法 | Usage](#使用方法--usage)
    - [1. clone仓库](#1-clone仓库)
    - [2. 进入相应文件夹](#2-进入相应文件夹)
    - [3. 更改文档内容](#3-更改文档内容)
    - [4. 提交代码到main分支](#4-提交代码到main分支)
  - [给新人：如何向X-DIY添加内容](#给新人如何向x-diy添加内容)
    - [1. 将本仓库Fork到你的账户中：](#1-将本仓库fork到你的账户中)
    - [2. 将你账户中的仓库Clone到本地](#2-将你账户中的仓库clone到本地)
    - [3. 做出更改，Commit到你自己的仓库](#3-做出更改commit到你自己的仓库)
    - [4. 提交Pull Request](#4-提交pull-request)
    - [5. 等待管理员手动将Pull request整合到主页面中](#5-等待管理员手动将pull-request整合到主页面中)


网页地址：[https://zjuxlab.github.io/X-DIY/](https://zjuxlab.github.io/X-DIY/)
## 分支 | Branches
* `main`：文档主要内容
* `webhooks`：用于构建GitHub WebHooks，实现自动同步更新网页内容
* `gh-pages`：`site`目录内容，用于构建GitHub Pages

> 说明：我们在开发网页过程中只需要维护main分支，其余两个分支我已配置好，不用更改。
## 架构说明 | Tree
* `wiki`目录存放网页内容，`wiki/docs`中的Markdown文件表示网页显示的内容，其结构可在[官方文档](https://markdown-docs-zh.readthedocs.io/zh-cn/latest/)中查看。
## 使用方法 | Usage
### 1. clone仓库
```bash
git clone https://github.com/zjuxlab/X-DIY.git
```
### 2. 进入相应文件夹
```bash
cd X-DIY
```
### 3. 更改文档内容
> 说明：采用Markdown语法，如插入图片推荐在仓库中单独开一个`assets`目录用于存储图片，后续由@SuperbRa1n统一上传到图床，也可以自行采用对象存储的方式（详见[内训文档](https://xn4zlkzg4p.feishu.cn/docx/KqqidOcw4oiCKtxhLxjcO7PRnNe?from=from_copylink)的`3.2.2`部分）。
### 4. 提交代码到main分支
```bash
git add .
git commit -m"commit messages"
git push origin main
```

> 说明：提交过后服务器自动更新网页可能有一定的延迟。

## 给新人：如何向X-DIY添加内容

### 1. 将本仓库Fork到你的账户中：

![fork1](readme/fork1.png)

![fork2](readme/fork2.png)

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
![request1](readme/request1.png)

![request2](readme/request2.png)

往下翻一点，你可以在提交前检查更改的内容。
![request3](readme/request3.png)

### 5. 等待管理员手动将Pull request整合到主页面中
注意：你可以自己建新页面，也可以修改已经存在的页面，或者将某个页面移动到其它分类下——你想怎么改就怎么改，管理员会**人肉**处理任何可能的冲突。