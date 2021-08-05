# 1.  Web入门

---

---

## 1.1  信息搜索

### 1.1.1  敏感目录泄露

#### 1.1.1.1  git泄露

**【漏洞简介】**git 是一个主流的分布式版本控制系统，开发人员在开发过程中经常会遗忘.git 文件夹，导致攻击者可以通过.git 文件夹中的信息获取开发人员提交过的所有源码，进而可能导致服务器被攻击而沦陷。

1. 常规git泄

   工具:https://github.com/denny0223/scrabble
   使用方法:

   ```bash
   ./scrablle http://example.com/
   ```

2. git回滚

   先利用 scrabble 工具获取源码，再通过 

   ```bash
   git reset--hard HEAD^
   ```

   命令跳到上一版本（在 git 中，用 HEAD 表示当前版本，上一个版本是 HEAD^），即可获取到源码

   除了使用 “git reset”，更简单的方式是通过 “git log-stat” 命令查看每个 commit 修改了哪些文件，再用 “gitdiff HEAD commit-id” 比较在当前版本与想查看的 commit 之间的变化。

3. git分支

    GitHacker（https://github.com/WangYihang/GitHacker）工具为例。GitHacker 的使用十分简单，只需执行命令 

   ```bash
   python GitHacker.py http://127.0.0.1:8000/.git/
   ```

   运行后，我们会在本地看到生成的文件夹，进入后执行

   ```bash
   git log --all
   ```

   或

   ```bash
   git branch -v
   ```

    命令，只能看到 master 分支的信息。如果执行 

   ```bash
   git reflog
   ```

   命令，就可以看到一些 checkout 的记录,除了master分支的信息还有一个secret分支,手动下载sercet分支的head信息,保存到.git/refs/heads/secret中,执行命令

   ```bash
   wget http://127.0.0.1:8000/.git/refs/heads/secret
   ```

   恢复 head 信息后，我们可以复用 GitHacker 的部分代码，以实现自动恢复分支的效果。在 GitHacker 的代码中可以看到，他是先下载 object 文件，再使用 git fsck 检测，并继续下载缺失的文件。此处可以直接复用检测缺失文件并恢复的 fixmissing 函数。

4. git泄露的其他应用

   除了查看源码的常见利用方式，泄露的 git 中也可能有其他有用的信息，如.git/config 文件夹中可能含有 access_token 信息，从而可以访问这个用户的其他仓库。

#### 1.1.1.2  SVN泄露

SVN（subversion）是源代码版本管理软件，造成 SVN 源代码漏洞的主要原因是管理员操作不规范将 SVN 隐藏文件夹暴露于外网环境，可以利用.svn/entries 或 wc.db 文件获取服务器源码等信息。这里有两个工具：

**dvcs-ripper** **https://github.com/kost/dvcs-ripper**

**Seay-svn**   https://github.com/Introspelliam/tools（Windows 下的源代码备份漏洞利用工具）。

#### 1.1.1.3  HG泄露

在初始化项目时，HG 会在当前文件夹下创建一个.hg 隐藏文件夹，其中包含代码和分支修改记录等信息。这里推荐工具：https://github.com/kost/dvcs-ripper。

