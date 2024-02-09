# X-DIY
X·Lab EE自学手册项目，基于mkdocs+GitHub WebHooks，实现了自动同步git push自动更新网页内容。

网页地址：[https://zjuxlab.github.io/X-DIY/](https://zjuxlab.github.io/X-DIY/)
## 分支 | Branches
* `main`：文档主要内容
* `webhooks`：用于构建GitHub WebHooks，实现自动同步更新网页内容
* `gh-pages`：`site`目录内容，用于构建GitHub Pages

> 说明：我们在开发网页过程中只需要维护main分支，其余两个分支我已配置好，不用更改。
## 架构说明
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