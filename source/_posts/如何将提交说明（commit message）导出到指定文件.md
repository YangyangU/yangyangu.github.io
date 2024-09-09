---
title: 如何将提交说明（commit message）导出到指定文件？
date: 2024-06-26 16:00:00
tags: [Git]
categories: Git
index_img: /img/git.png
banner_img: /img/default.png
---

## 前言

公司里总会有一些有趣的需求，这不，mentor：“你去调研一下，看怎么把 commit 提交说明导出到一个 txt 文件里，条目的话最好可以指定，后续我们……”。刚开始接到这个任务还是挺忙蒙的，网上应该有这个功能的第三方包吧？但是在我查询多方资料，发现 Git 真 🐂，哪需要什么第三方包！

我在一个 git 学习的网站看到这句话真绷不住，赶紧跟大家分享一下

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/841eec7d5078487ebc2f15ba3a5a8756~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=842&h=130&s=15474&e=png&b=fefefe" alt="image.png" width="70%" />

假如我真的学完了 16 章的内容，然后你跟我说还没入门？这我学集贸啊 😂。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9d7c8aae7eb4f68b6268f1ec3c72adf~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=321&h=737&s=36251&e=png&b=fefefe" alt="image.png" width="30%" />

行了，闲话少说，我们步入正题，开始今天的分享。

## 拿到提交说明

我们如果想查看`commit`的内容，可以`git show`查看详细信息，它可以显示最新提交的详细信息，包括作者、日期、提交说明和每个文件的变更内容。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/08cabde444e440018e9a95c357768b6d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=665&h=294&s=21106&e=png&b=1e1e1e)

但是这里只能展示最新提交，而我们的需求是可以指定条目的 commit 提交说明，而这里只显示一条，显然不满足需求。

然后又找到`git log`，允许我们查看 git 仓库的提交历史，它会按照时间顺序列出提交记录，并可以显示提交的哈希值、作者、日期、提交信息等信息。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4a5de345be0e492b8e76588ff5ac604e~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=693&h=376&s=27111&e=png&b=1e1e1e)

这不正是我们想要的吗，看看怎么单独拿到提交说明。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b4a6454cd0d4d609d35f1b35ece3f57~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=674&h=200&s=29029&e=png&b=fbfbf8)

[git 文档](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History)中提到了`--pretty`这个选项，它允许日志输出默认格式以外的格式！如果我可以将输出只显示提交说明那不是美哉，于是我继续看文档。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1719fc5f89d4d4fa885d63a6f3e7628~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=671&h=230&s=24282&e=png&b=f7f6f5)

然后选项值为`format`时，我们就可以指定输出格式，它提供一些占位符允许我们自定义输出，文档中也对每个占位进行列举。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1064fd81ccbc41e6bfb34f69fff7a85f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=440&h=773&s=30905&e=png&b=fcfcfa)

其中`%s`就是我们需要的提交说明，试试看！

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1fd5492cbdcd4ab485408e2778e7ad4e~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=387&h=139&s=6332&e=png&b=1e1e1e)

果然成功拿到！成就感爆棚了。

## 导出到指定文件

我们离成功只差一步，现在我们只是在终端看到结果，怎么将终端的数据导到本地文件呢？

git 有一个打包文件的方法`git archive`，这个命令本身并不支持包含提交说明字段的功能。它主要用于将指定的提交、分支或标签的代码打包成归档文件，主要关注代码本身而非提交历史，而不包含 Git 仓库的元数据（如提交历史、提交说明等），所以行不通。

查了一些资料，发现已经用不着 Git 了，既然是在控制台显示，那我就用控制台的方法呗。

### 重定向操作符`>`，`>>`

在大多数命令行环境中，可以使用`>`将控制台输出保存到文件中

```bash
git log > log.txt
```

这行命令将会把我们的日志保存到当前目录下的`log.txt`文件中，如果已经存在`log.txt`文件则会进行覆盖操作。

而`>>`则会将输出追加到文件末尾，而不覆盖原有内容。

### PowerShell 脚本

如果你使用 PowerShell，也可以通过脚本来执行 Git 命令并将输出进行保存

```powershell
git log | Out-File -FilePath log.txt
```

---

因为我是用 VScode，它自带的就是 PowerShell，所以就用 PowerShell 脚本给大家进行展示

```bash
git log --pretty=format:"%s" | Out-File -FilePath commit_messages.txt
```

执行完命令，我们可以在当前目录下看到新生成的`commit_messages.txt`文件。

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04482d43ef534b0893bb07858a5d6e02~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=391&h=411&s=39493&e=png&b=1e1e1e" alt="image.png" width="50%" />

当当，成功导入 txt 文件，但是我们发现文件里的中文全是乱码，为什么会这样？

## 解决中文乱码

**1. 在导出的时候明确编码**

```bash
git log --pretty=format:"%s" | Out-File -FilePath commit_messages.txt -Encoding utf8
```

但是发现并不奏效

**2. 设置 Git 输出编码**

```bash
git config --global i18n.logOutputEncoding gb2312
```

这里我将 git 输出的编码格式改为 gb2312，再次执行导出操作，发现乱码消失。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0b2830415434d2788d899f0ad0cf3ea~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=324&h=355&s=27528&e=png&b=1e1e1e)

## 指定输出条目和格式

现在我们想实现输出最新的 20 条数据，并且标上序号（如`1.`）

想要指定条目，我们只需加上`-n + 数字`

```bash
git log -n 20
```

这里表示指定输出最新的 20 条日志信息

加上序号的话，我们可以创建一个计数器，然后对每行内容进行拼接

```bash
$counter = 1
git log -n 20 --pretty=format:"%s" | ForEach-Object {
    "$counter. $_" | Out-File -FilePath commit_messages.txt -Append
    $counter++
}
```

`$counter = 1`初始化一个计数器，初始值为 1

`ForEach-Object{}`是对输出的每一条进行单独的操作，这里进行拼接操作

`-Append`将内容追加到文末，而不是覆盖

**最终效果**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4e44519d6f84090991db1aa63ffc250~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=671&h=605&s=65520&e=png&b=1e1e1e)

---

调研完，不出意外的就要出意外了……

mentor：你现在再调研一下然后实现两种方式：

可以指定到具体更新了多少内容，比如说可能有两种情况：用 tag 进行区分，上次的 tag 到本次 tag 之间的 commit messages，对这个区间进行导出；另一种情况就是，拉一个新分支进行开发，新分支的最后一次提交到主分主的最后一次提交，这个区间进行导出。

这些我们后期会针对不同的场景进行实施……

## 一些前驱知识

### 区间符..

`..`允许我们指定输出的区间

```bash
git log master..HEAD
```

输出从 master 的最新提交到当前项目的 HEAD

### ^操作符

`^` 操作符用于指定父提交

父提交指一个提交的上一个提交。每个提交都有一个或多个父提交，具体取决于它是普通提交还是合并提交。

#### 普通提交

对于普通提交，每个提交只有一个父提交。

```css
A---B---C---D
```

- 提交 `D` 的父提交是 `C`。
- 提交 `C` 的父提交是 `B`。
- 提交 `B` 的父提交是 `A`。

在这种情况下：

- `D^` 或 `D^1` 表示提交 `D` 的父提交 `C`。

#### 合并提交

合并提交是指将两个或多个分支合并在一起时产生的提交。合并提交有两个或更多的父提交。

```css
A---B---C---E  (main)
     \     /
      D---F   (feat-test)
```

- 提交 `E` 是一个合并提交，合并了 `feat-test` 到 `main`。

- 提交 `E` 有两个父提交：

  - 第一个父提交 `C`，表示 `main` 分支在合并前的最后一个提交。
  - 第二个父提交 `F`，表示 `feat-test` 的最后一个提交。

在这种情况下：

- `E^1` 表示提交 `E` 的第一个父提交 `C`。
- `E^2` 表示提交 `E` 的第二个父提交 `F`。

搞清楚这些，那我们的工作就好开展了

## 使用 Tag 进行区间导出

假设你有两个 tag，分别为 `v1.0` 和 `v2.0`，你想导出这两个 tag 之间的 commit messages

```bash
$counter = 1
git log v1.0..v2.0 --pretty=format:"%s" | ForEach-Object {
    "$counter. $_" | Out-File -FilePath commit_messages.txt -Append
    $counter++
}
```

`v1.0..v2.0`就可以指定两个 tag 之间

## 从新分支的最后一次提交到主分支的最后一次提交

假设你有一个新分支 `feat-test` 和主分支 `main`，这种就要分情况了，可能是合并前也可能是合并后

### 合并前

```bash
#合并前
$counter = 1
git log main..feat-test --pretty=format:"%s" | ForEach-Object {
    "$counter. $_" | Out-File -FilePath commit_messages.txt -Append
    $counter++
}
```

### 合并后

合并后的话只需要将开始指向合并前的最后一次提交，不就是 main 的第一个父提交吗

```bash
git log main^..HEAD
```

over

## 参考

- [How do I export a Git log to a text file?](https://stackoverflow.com/questions/10330425/how-do-i-export-a-git-log-to-a-text-file)
- [Git 官方文档](https://git-scm.com/docs)
- [learn-git-the-super-hard-way](https://b1f6c1c4.gitbook.io/learn-git-the-super-hard-way)

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
