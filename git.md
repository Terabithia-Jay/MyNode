## 指令

- git 就是代码的管理工具，也可以说是代码的仓库

    `git -v`    ：查询有没安装成功

    `git init`   ：初始化仓库（文件夹下会自动创建一个隐藏的文件夹，实际开发不太需要初始化，因为项目在远程仓库，只需要克隆过来即可）

    `git log`   ：查看提交的日志

## 配置

- 先配置(name，email。提交代码到git仓库的标识)

    ​    `git config --global user.name "wangjie"`

    ​    `git config --global user.email "317243444@qq.com"`

## 文件状态

- 文件状态（用 git status 查看该目录下的文件的文件状态）

  1. 未跟踪：指文件没有被git管理

  2. 已跟踪：被git管理

     1. 暂存：表示文件已经修改并存到暂存区，但未提交到git仓库（暂存区就是在目录下的隐藏文件的一部分，隐藏文件称为版本库）

     2. 未修改：表示磁盘中的文件和git仓库中的文件相同，没有修改

     3. 已修改：表示磁盘中文件已被修改，和git仓库中的文件不同

        

- 刚创建到项目中的文件处于未跟踪状态：
  1. 未跟踪 --> 暂存
     1. `git add 文件全名`
     2. `git add *`   ：把所有未跟踪或已修改的文件全部添加到暂存区
  2. 暂存 --> 未修改    将暂存区的文件提交储存到git仓库中（创建版本记录）
     1. `git commit -m` ："增加了登录功能"      这里的XXXX是写日志
  3. 未修改 --> 已修改
     1. 修改代码后，文件会变为已修改状态
  4.  已修改 --> 暂存
     1. `git add 文件全名`   ：把文件添加到暂存区

​      `git commit -a -m`： "增加了3个功能"   表示把所有的已修改的文件变为未修改(除了未跟踪的文件)，等于是执行了 `git add *` 和 `git commit -m "XXXX"` 这两步



- 常用命令：

​      `git restore .\1.txt`    ：恢复文件，回到上一次未修改（上一次commit）状态时的样子

​      `git restore --staged .\1.txt`   ：取消暂存状态（取消修改，然后再 `git restore .\1.txt` 恢复文件）

​      `git rm .\1.txt`  ：删除文件，命令后面加一个 -f 表示强制删除（已修改状态下不能rm删，只能强制删）



## 分支

​    `git branch`  ：查看分支

​    `git branch text`    ：创建一个text分支

​    `git branch -d text`  ：删除text分支

​    `git switch text`    ：切换到text分支

​    `git switch -c text`  ：创建一个text分支并切换到text分支



- 每次commit的时候都会创建一个对应的节点，默认分支是master，会指向最新commit那个节点

- 创建分支表示在这个节点复制一份项目，在此项目编辑不影响主分支master的代码，这个分支指向自己最新commit的项目



### 合并分支（merge）

- 在开发中都是在自己的分支上编写代码，代码编写完成后，在将自己的分支合并到主分支master上
  1. 先切换到主分支 `git switch master`
  2. 然后 `git merge text`  将text分支和主分支master合并
  3. 删除合并的分支 git branch -d text，然后主分支master就会指向这个合并的项目 



## 变基（rebase）

- 变基解决分支太多混乱

1. 当我们发起变基时，git会首先找到两条分支【最近】的共同祖先
2. 对比当前分支相对于祖先的历史提交（对比两个版本代码有何不同），并将他们提取出来储存到一个临时文件里
3. 将当前部分指向目标的基底
4. 以当前基底开始，重新执行历史操作



​    `git rebase test`



- 大部分情况下合并和变基是可以互换的，但是如果分支已经提交给了远程仓库，尽量不要使用变基

- 如果要使用变基要在本地变基完再上传到远程仓库



## 远程仓库

​    `git remote` ：查看当前关联的远程库

​    `git remote remove origin`   ：删除远程库

- 私有库：公司用

- 共库：开源，大家都能看到：GitHub、Gitee



​    `git remote add origin https://github.com/Terabithia-Jay/git-demo.git`     ：关联自己的仓库地址并命名为origin

​    `git branch -M main`                              ：修改分支master的名为main（可以不改）

​    `git push -u origin main`                            ：将代码上传到服务器上



- 下载别人代码：复制github链接，`git clone` 链接   

- 下载远程仓库代码并改名文件夹：如果 `git clone` 链接 自己取名

  

- 多人开发时，如果本地库的版本节点跟远程库的版本节点不一致，默认是不能推上去远程库的

- 如用cmd需要先更新本地库：`git fetch` 或 `git fetch origin`

- 如果本地库和远程库有文件名一样的，但内容不一样，用`git fetch`并不会将远程库代码和本地库的代码合并
  - 所以我们git fetch下载代码后要手动合并：`git merge origin/master`

- 推送代码之前，要先确保本地库跟远程库的版本节点一样，要先从远程库中拉取最新的代码

- `git pull`   ：从远程库拉取代码并自动合并，等于是用了 fetch 和 merge



- `git push origin master:hello`    ：将本地的master分支推送到远程库的origin下的hello分支





- 切换版本节点到之前的版本（分离头指针：HEAD指针）

- 不要在分离头指针的状态下进行操作仓库

- 如果非要回到之前的版本操作，要先创建分支后再进行操作，先`git log`查看版本日志，找到随机生成的版本id
  - `git switch -c test 版本id`

- 可以为提交日志设置标签，设置后可以通过标签快速切换到那个版本，但也有分离头指针的问题，所以也要先创建分支
  - `git tag v1.0`         ：为当前节点设置名为v1.0的标签
  - `git tag v1.0 版本id`      ：为当前节点设置名为v1.0的标签
  - `git switch -c test v1.0`    ：切换到v1.0标签的节点并创建test的分支
  - `git push origin v1.0`     ：将v1.0版本节点推到远程仓库
  - `git push origin --tags`    ：将所有标签推到远程仓库
  - `git tag -d v1.0`        ：删除v1.0的标签节点
  - `git push origin --delete v1.0`   ：删除远程仓库名为v1.0的标签 



## gitignore

- 默认情况下git会监视项目中所有的内容，但是node_modules目录是不需要被监视管理的，可以在项目目录中新建一个文件

- .gitignore 文件

  - 里面一行写一个文件名
  - *.log   目录下所有文件都不监视

  

###   github静态页面

- 可以将自己的静态页面部署到github中，它会给我们提供一个地址使得我们的页面变成一个真正的网站，可以让别人访问

- 要求静态页面的分支（远程仓库名）必须叫做：gh-pages，然后通过 terabithia-jay.github.io/github的库名（比如demo）访问

- 如果希望页面可通过 terabithia-jay.github.io 访问，则需要将github的库名创建的时候就配置为 terabithia-jay.github.io 而不是demo





##   静态网页框架网站

 https://docusaurus.io

​    1、安装

- `npx create-docusaurus@latest my-website classic`

​    2、启动项目	

- `npm run start`

​    3、配置项目

- 文件：docusaurus.config.js  在这里面改网站的数据

​    4、构建项目

- `npm run build`

​    5、指定要部署的分支名

- 添加这个属性名：deploymentBranch: "gh-pages",

​    6、配置环境变量

- GIT_USER       terabithia-jay

​    7、将build文件部署到github仓库

-  `npm run deploy`

​    