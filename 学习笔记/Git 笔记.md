## 基本概念

**工作区（Working Directory）：**

**就是在电脑里能看到的目录，目录即文件夹**

**暂存区：**

**Git 的版本库有个叫 stage（或者叫 index）的暂存区，像个缓存区域，临时保存你的改动。**

**Git 会自动创建第一个（默认）分支 master，以及指向 master 的一个指针叫 HEAD。**

**HEAD 指向当前分支，是你最后一次提交的结果**

**所以暂存区又名索引，是数据暂时存放的区域，对于 add git版本控制的文件，就算是进入暂存区啦。**

**可以理解为数据进入本地代码仓库之前存放的区域。由于还没对本地仓库生效，所以是数据暂时存放的区域。**

**存放在 ".git目录下" 下的 index文件（.git/index）中。**

**版本库（Repository）：**

**版本库又名仓库、本地仓库，英文名repository，工作区有一个隐藏目录.git，就是 Git 的版本库。**

**其中版本库里面存了很多东西，其中最重要的就是 stage (暂存区)，**

**存放在工作区中“.git”目录下**

**在暂存区 commit 的代码会被放入版本库中。可以理解为一个本地的代码仓库。**

![3 [1].jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1656301035577-82be8249-4eca-49a4-8616-8a7570a89b1c.jpeg)

git add

数据从工作区转移至暂存区

git commit

数据从暂存区转移至版本库 .git（工作区隐藏目录），也就是本地仓库

git push

数据从版本库中发送到远程仓库

![3.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1656301036805-c16cef3c-d880-485f-b687-57dd4cae5c50.jpeg)

## 配置git

```bash
git config --global user.name "vdbvxcbcb"
git config --global user.email "2067659951@qq.com"
git config --global push.default matching
git config --global core.quotepath false
git config --global core.editor "vim"
```

## 配置github公钥

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301037185-1d76e7a1-ad94-4433-855d-eda9c64188ab.png" alt="3.png" style="zoom: 33%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301037285-0e3b9ae9-5738-4144-93f9-ab1fddcd4c7e.png" alt="4.png" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301037518-0cb03398-92e3-4ccf-8115-56e2242c6740.png" alt="5.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301038639-ee7ec0ec-6d44-4e1f-bfed-36259807ce01.png" alt="6.png" style="zoom: 33%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301038867-b8a756b2-cc7d-40d5-8c88-0c6000f284e5.png" alt="7.png"  />

![8.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301039185-da9d9449-f309-4d71-88fb-2f9ceae39ac4.png)

![9.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301039445-8c80b9b6-9c2f-4413-a684-28d131ea9053.png)

![10.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301039554-9739cdfa-6540-4921-9e5b-1100183e212f.png)

![11.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301039827-630f1974-5ef5-4157-a8fd-a7949703b68b.png?x-oss-process=image%2Fresize%2Cw_1022%2Climit_0)

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301040118-15dc7e98-d4a2-49f4-b909-f6b48ca47300.png" alt="12 [1].png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301041175-acadf751-9846-4989-9cfb-8133b21da5a5.png" alt="12.png" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301041347-02cb09e1-58ff-4c8b-8e20-4e67fa04b1b1.png" alt="13 [1].png" style="zoom:67%;" />

![14.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301042735-b96d3437-35fb-4a27-8ee0-f8e0bf226a55.png)

![15.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301042816-d620bd7f-0fde-49c9-8f81-2c9d87eb9a68.png)

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301043272-313d5281-1e10-4372-b1d8-a3ae5d311baf.png" alt="16.png" style="zoom: 33%;" />

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1656301043211-55b48d94-6d5b-4a1b-8e03-d9ae2e74d661.png" alt="17.png" style="zoom: 67%;" />

```bash
打开git bash

ssh-keygen -t rsa -b 4096 -c "1355498705@qq.com"  (生成公钥)

这个Key也不是用于军事目的，所以也无需设置密码

回车三次即可

cat ~/.ssh/id_rsa.pub      复制id_rsa.pub文件的内容粘贴到github相关页面

yes

添加公钥并克隆仓库到本地后

cd 远程仓库名 
ls
README.md        本地已有README.md文件   
```

注意id_a_github 的名字不能和你原来私钥的名字相同

.pub（public）文件是公钥，没有.pub的是私钥

## 工作 Git 

![截屏2020-12-26 下午2.12.13.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302472002-c815e938-ac80-4076-8154-d657e91794ff.png)

https://backlog.com/git-tutorial/cn/

https://zhuanlan.zhihu.com/p/263000017

fork 公司项目仓库

### Sourcetree 克隆仓库

打开 sourcetree ，选择 + 打开新标签页， 从 URL 克隆 输入复制的 https 地址

https://www.wangquanwei.com/68.html

git 配置用户名和邮箱，生成 SSH key ，添加到个人仓库，删除了不会影响其他项目但一定要有一个：

https://segmentfault.com/a/1190000009572470?_ea=1985120
https://blog.csdn.net/mqdxiaoxiao/article/details/92712143
https://www.jianshu.com/p/32b0f8f9ca8e

Sourcetree 加一个自己的源（一般设置两个源，一个是自己的，一个是公司公共的）

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302471516-41a683ed-e114-41a8-bf80-1ce01a565f07.png)

鼠标移动到自己的远程仓库：

![image (3).png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302470877-2dc878b6-62ab-4afb-bdb2-0cad6adeac7c.png)

右键第一个拉取：

![image (1).png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302470171-38aab47d-1f58-4a20-9c02-d02482db1660.png)

然后检出就是自己的分支了：

![image (2).png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302470484-39ac3b91-fb7d-4155-a11e-439595ff4f70.png?x-oss-process=image%2Fresize%2Cw_558%2Climit_0)

遇到下列错误，个人 fork 的仓库增加协作者，授权即可

![image (4).png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302470881-349cc2c3-07ef-4829-a348-d6e310bf0577.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

**SourceTree 下同一颜色表示同一分支（每次提交后颜色可能会变），分支中每一个点代表该分支的一次提交，最新的提交总是在最上面**

接到需求 => 本地开发完成、自测（检查有没有影响到其他功能） => 提交 pr 到测服环境（develop）=> 项目经理产品验收 => pr 到预发布环境（pre）=> 自测，验收 => pr 到正式环境（master）=> 打版本号（master 每次修改都要改）=> 自测，验收

基于 master 创建新的功能分支，检出到功能分支开发，比如 xt 分支，

每次本地分支推送到自己的远程分支后，

**要 PR 到相同的远程公共分支前，注意拉取 pull 一下远程公共分支，自己查看是否有合并冲突！**

如果中间没有 pull 这一步，直接 push，有了冲突还是得 pull 来解决冲突。

放到已暂存区，添加备注，点击提交，推送到远程分支后合并到其他分支：develop、pre、master，一次合并一个。

本地分支合并之后推送到远程仓库发 PR 验收即可。



### commit 提交规范

#### commit message格式

```html
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

1. scope: commit 影响的范围, 比如: route, component, utils, build...

2. subject: commit 的概述

3. body: commit 具体修改内容, 可以分为多行

4. footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接

大致分为三个部分(使用空行分割):

1. 标题行: 必填, 描述主要修改类型和内容

2. 主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等等

3. 页脚注释: 可以写注释，BUG 号链接

#### type: commit 的类型

feat: 实现新功能、新特性、新行为的 commit

perf: 优化相关，比如提升性能、体验（在不影响代码内部行为的前提下，对程序性能进行优化），

perf 可以归到 feat，毕竟性能也是一种 feature。

fix: 修改 bug

refactor: 代码重构，没有加新功能或者修复 bug（重构，在不影响代码内部行为、功能下的代码修改）

chore: 中文翻译为日常事务、例行工作，日常维护性的改动，其它修改（不在上述类型中的修改）

比如改变构建流程、或者增加依赖库、工具等

docs: 文档修改，比如 README, CHANGELOG, CONTRIBUTE 等等



style: 代码风格/代码格式的修改, 注意不是 css 修改（例如分号修改、添加注释）

revert: 恢复上一次提交，回滚到上一个版本

merge：代码合并

release: 发布新版本

ci: 持续集成相关文件修改

test: 测试用例新增、修改

build: 影响项目构建或依赖项修改

workflow: 工作流相关文件修改



### 分支管理规范（参考）

一个项目可以创建两个分支：master 和 dev。master 对应线上分支，不能直接在 master 分支上写代码，开发时需要从 master 上拉一个 dev 分支进行开发。


分支1：feature hotfix dev pre master

分支2：release hotfix dev pre master

https://juejin.cn/post/7096866638095450119

开发的时候一般一个需求一个分支, 最后可能只合 dev 不合 release

hotfix 都从 release 切的，修完把 hotfix 合 release, 再把 release 合到 dev 分支



#### 开发新功能

当团队成员开发新功能时，需要从 dev 上拉一个 feature-功能名称-开发姓名 分支进行开发，

例如：feature-login-tgz。开发完成后需要合并回 dev 分支。



#### 修改 bug

当团队成员修改 bug 时，需要从有 bug 的分支（环境）上拉一个 bug-功能名称-开发姓名 分支进行修复，例如：bug-login-tgz。

修复完成后需要合并回原来出现 bug 的分支。

以 feature 或 bug 开始的分支都属于临时分支，在通过测试并上线后需要将临时分支进行删除。

避免 git 上出现太多无用的分支。



#### 合并分支

**在将一个分支合并到另一个分支时（例如将 feature-* 合并到 dev），**

**需要查看自己的新分支中有没有多个重复提交或意义不明的 commit。**

**如果有，则需要对它们进行合并（git rebase）。**

示例：

```bash
# 这两个 commit 可以合并成一个
chore: 修改按钮文字
chore: 修改按钮样式

# 合并后
chore: 修改按钮样式及文字
```

注意：

在将分支合并到另一分支前，例如将 `feature/*` 合并到 `dev`。

需要先拉取 `dev` 的最新更新，然后回到 `feature/*`，执行 `git rebase dev` 操作，再提交，最后提合并分支操作。



##### VSCode 解决合并冲突

`<<< HEAD`

新的本地更改

`====`

旧的远程更改

`>>>>>>`

![F08A4EBF-C80C-4399-82E1-D4B4C6385D1E.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302469359-cd2467f0-29a1-4656-8a84-820c628601fa.png?x-oss-process=image%2Fresize%2Cw_621%2Climit_0)

本地分支合并后 PR 发生合并冲突，先拉取远程分支（要 pr 的分支）到本地分支，

Accept Incoming Change 表示使用传入（别人的）的更改。

Accept Current Change 表示使用本地当前（我的）的更改。

##### SourceTree  解决合并冲突

右键文件选择解决冲突使用谁的版本，文件变更栏可以看到 updated upstream 是别人改的版本，+==== 下面是我改的版本。

如果远程和本地都有问题，建议打开 VSCode 编辑后提交。

#### Push 冲突与解决

![git.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1656302469309-dd1c63ad-98bf-4a71-8173-abd8291de4cc.jpeg)

![715AC968-6F15-4650-B8C2-06FE09C598CD (1).png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302469482-ddcfbcb6-a373-4450-a262-555149b9ff53.png?x-oss-process=image%2Fresize%2Cw_599%2Climit_0)

GitHub 的远端仓库上含有本地仓库没有的内容，所以这次 push 被拒绝了。

这种冲突的解决方式其实很简单：先用 pull 把远端仓库上的新内容取回到本地和本地合并，然后再把合并后的本地仓库向远端仓库推送。

看到吧，发生了错误，推送被拒绝（rejected）了。

![截屏2020-12-29 上午9.16.33.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302472273-fabc7ad5-09f3-4912-ad00-f9c3d98ddf77.png?x-oss-process=image%2Fresize%2Cw_619%2Climit_0)

![截屏2020-12-29 上午9.16.54.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302472285-2924d54c-810c-4c86-8500-7ce3be7fe619.png?x-oss-process=image%2Fresize%2Cw_603%2Climit_0)

##### 解决（避免冲突的习惯：先 pull 再 add）

**推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，**

**Git 已经提示我们，先用 git pull 把最新的提交从 origin/marster 拉取下来，然后，在本地合并，解决冲突，再推送。**

**因为当前本地分支是落后于远程分支的副本，需要在推送之前合并远程的修改**

**总结：如果 push 失败，就用 pull 把本地仓库的提交和远程仓库的提交进行合并，然后再 push 一次**



git pull

在上面的文段里，我已经举过一次 git pull 的例子，

但这次的 git pull 操作并没有像之前的那样直接结束，而是进入了上图这样的一个输入提交信息的界面。

这是因为当 pull 操作发现不仅远端仓库包含本地没有的 commits，而且本地仓库也包含远端没有的 commits 时，

它就会把远端和本地的独有 commits 进行合并，自动生成一个新的 commit ，

而上图的这个界面，就是这个自动生成的 commit 的提交信息界面。

另外，和手动的 commit 不同，这种 commit 会自动填入一个默认的提交信息，简单说明了这条 commit 的来由。

你可以直接退出界面来使用这个自动填写的提交信息，也可以修改它来填入自己提交信息。

这种「把不同的内容进行合并，生成新的提交」的操作，叫做合并，

它所对应的 Git 指令是 merge。

事实上，git pull 这个指令的内部实现就是把远程仓库使用 git fetch 取下来以后再进行 merge 操作的。

![截屏2020-12-29 上午9.22.48.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302472355-acf5a584-bf21-450b-8ede-8304cc78fd51.png?x-oss-process=image%2Fresize%2Cw_647%2Climit_0)

![截屏2020-12-29 上午9.23.02.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302473074-262a5c36-e17d-42cd-8f18-5e1ebffa817e.png)



#### 回滚某个文件

git log ./test.txt 查看文件相关的 commitId

暂存区所有的文件恢复到工作区修改之前的状态，相当于 VSCode 中的放弃更改：

未提交到暂存区或远程  git checkout --  ./test.txt

除此之外，

已经提交到远程 git checkout commitId ./test.txt  切换到这个commit_id 上，文件的内容就是这个 commit 保存的内容

commitId 不必写全，写前几位，例如 7 位



Sourcetree 回退只是提交但未推送到远端（提交保留在本地的情况）

直接混合合并重置到选定的之前的提交

不想重置到选定的之前的提交，那就再次混合合并重置到之后的提交



#### 更改文件名大小写

Git 修改了文件名大小写后，远端将不识别对应文件。

例如：远端服务器`a.html`，本地重命名后`A.html`，提交后远端还是`a.html`。

操作步骤 请按顺序执行如下命令：

```sh
git mv --force a.html A.html
git add A.html
git commit –m "rename"
git push origin XXX(分支)
```

#### 在某个分支修改了，但是想换个分支提交？(也可以 cherrypick)

以下操作前提是：release 是从 master 切出来的，否则只能从 master 再切个新分支，然后再操作

比如修改了的分支为： master。想换到 release 分支提交，

在 master 先 commit ，切换到 release 分支 ，合并 master 到 release


突然之前没提交过的一些功能要上线，但现在工作区本地正在开发一些未需上线的新功能（新旧功能混在一起）

feature 将需要保存的部分文件放到暂存区，然后贮藏（贮藏功能对其它分支也有用，应用到其它分支时会直接放在暂存区）

回滚并提交 pr 后，使用应用贮藏将文件拉到 feature 分支的工作区继续开发新功能

#### 解决提交到错误的分支

```sh
#未提交的情况下
git add . #把所有改动暂存

git stash #把暂存的文件提交到git的暂存栈

git checkout 本该提交代码的分支

git stash pop #将暂存栈中的代码放出来

#至于是继续改还是提交就随你了

#已提交的情况下
git checkout 不该提交代码提交了代码的分支

git reset HEAD~1 #最近一次提交放回暂存区, 并取消此次提交

git stash #把暂存的文件提交到git的暂存栈

git checkout 该提交代码的分支

git stash pop

#下面一顿操作随你猛了，等你把代码提交到了正确的分支后，再次切到刚刚错的分支

git push origin 错误的分支 -f (把不该上去的文件回退掉)
```



#### Sourcetree 分支回退已 push 到自己远端的提交（内容和提交记录都回退）

**建议回滚前先做好代码备份。**

```sh
git checkout . #本地所有修改的、没有提交的文件都返回到原来工作区修改之前的状态，即所有文件放弃更改
```

**注意：如果在你重置提交之前，有未拉取的代码，应该把拉取完成之后再commit。**

**否则经过上面的操作，会把别人提交的代码回退回去！！！**

**reset 是重置到提交**

**软合并是指将此次提交回滚到指定提交位置，这个过程中会将修改过的文件回退到暂存区。**

**混合合并是指将此次提交回滚到指定的位置，这个过程中将修改过的文件回退到未暂存文件区。**

**强行合并是指将此次提交回滚到指定的位置，但这个过程中将直接丢弃之前修改的所有文件，**

**因此在选取此种合并时需要考虑清楚，避免一些不必要的麻烦。**

| 模式名称 | HEAD的位置 | 暂存区 | 工作区 |
| :------: | :--------: | :----: | :----: |
|   soft   |    修改    | 不修改 | 不修改 |
|  mixed   |    修改    |  修改  | 不修改 |
|   hard   |    修改    |  修改  |  修改  |


远程分支有些提交不想在 pr 时提交但是已经推送到远程，可以选择回退，操作过程如下：

**举个例子，**

**在自己的本地 release/v1.0 分支下，重置当前分支到此次提交，**

**直接混合合并回退到历史的某次提交 ，**

**注意：这时会发现你有x个提交可以拉取，如果拉取就会恢复到之前的样子。**

**之后再 git push origin release/v1.0 -f**

因为自己的本地分支回滚后，版本将落后自己的远程分支，必须使用强制推送覆盖远程分支，否则无法推送到远程分支

**或者**

**选择混合合并回退到历史的某次提交后，丢弃本地所有提交，（这时不想回滚了也可以重置当前分支到此次提交，混合合并模式）**

**在自己的远程仓库下右键删除对应的自己的远程分支 release/v1.0，重新推送到自己的本地 release/v1.0 到远程，即可回滚**

参考：

https://blog.csdn.net/woshisunyizhen/article/details/105127450

https://blog.csdn.net/weixin_38357164/article/details/103598155

https://shuhao.blog.csdn.net/article/details/103619432

https://blog.csdn.net/wh710107079/article/details/103789947

https://blog.csdn.net/qq_30932479/article/details/85128311

https://juejin.cn/post/6844904089600409613

https://www.cnblogs.com/tugenhua0707/p/4050072.html

https://blog.51cto.com/u_13609606/3148540



#### SourceTree 回退已 push 到自己远端的提交，生成新提交（内容回退，提交记录不回退）

**建议回滚前先做好代码备份。**

**revert 是回滚提交，它是通过反向操作来完成撤销的。**

**如果需要将回滚提交更新到远端，push 即可，不需要 push -f。**

git revert 执行后会自动生成一个类似「Revert "commit message"」的新的commit。

该 commit 的内容和需要 revert 的内容相反。

若回退前新增了一个文件，revert 后会将该文件删除；

若回退前删除了一个文件的一行代码，revert 后会将该文件的该行代码补回来。   

如果需要将撤销更新到远端，push即可，不需要 push -f。

revert 命令的好处就是不会丢掉别人的提交，

即使你撤销后覆盖了别人的提交，

他更新代码后，可以在本地用 reset 向前回滚，找到自己的代码，

然后拉一下分支，再回来合并上去就可以找回被你覆盖的提交了。

**一般来说，git revert 更安全，但也会生成新的 revert commit，如果撤销的 commit 很多的话，git log 不是很好看**

**当然，也有办法优化，可以在使用 git revert  依次回退多个提交后，通过 git rebase 将多个 revert commit 合并成一个。**

**如果要撤销的 commit 还没被推到远端，不妨使用 git rebase 或者 git reset。**

**如果已经被推送到和他人共同维护的远端分支，或者已经被 merge 到主分支，最好使用 git revert。**

https://juejin.cn/post/6844904089600409613

https://juejin.cn/post/6844904089122275341



在本地 release/v1.0 分支下，

第一次重置选中想要回退的目标提交记录，使用模式一定要选中强行合并-丢弃所有工作副本改动

第二次重置选中最后一次提交记录，使用模式 (推荐) 软合并-保持所有本地改动

相应的变动在本地体现了。再点击提交一次，就把误操作的提交内容删掉了。

提交并推送所有改动文件。

注意：代码版本从内容上回退到了某个历史版本，但是提交历史记录并没有回退。回退指的是内容的回退，而不是提交记录的回退 。

相当于 revert 通过反向操作来完成撤销

https://juejin.cn/post/7005924785519591455

https://blog.csdn.net/hubuwch/article/details/119154093



#### 回滚总结


**远端分支回滚的三种方法：**

**自己的分支回滚直接用 reset**

**公共分支回滚用 revert**

**错得太远了直接将代码全部删掉，用正确代码替代**

如果公共远程分支回退了，自己的远程分支会显示 ahead ，

即自己的远程分支比公共远程分支多了 x 个提交，公共远程分支有丢掉的你的提交，

这时需要从自己的远程分支切个新的远程分支保存多出来的 x 个提交，

回到自己的远程分支， reset 重置到之前的提交，与公共远程分支保持一致，

然后自己的新的远程分支（多出来的 x 个提交）merge 到自己的远程分支

 **Reset, Rebase, Revert 三个指令的区别：**

| 指令   | 改变历史记录 | 适用场合                                                     |
| :----- | :----------- | :----------------------------------------------------------- |
| Reset  | 是           | 把目前的状态设定成某个指定的 Commit的状态，通常适用于尚未推出去的 Commit。 |
| Rebase | 是           | 不管是新增、修改、删除Commit 都相当方便，用来整理、编辑没有推出去的 Commit 相当方便，但通常也只适用于尚未推出去的 Commit。 |
| Revert | 否           | 新增一个Commit 来取消另一个Commit 的內容，原本的 Commit 依旧会保存在历史记录中。虽然会因此而增加 Commit 数，但通常比较适用于已经推出去的 Commit，或是不允许使用 Reset 或 Rebase 修改历史记录的指令的场合。 |

#### fix 紧急修复

![截屏2020-12-29 下午1.13.16.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302473377-842b5b84-c9a3-43e2-a6d5-92e5cefba90d.png)

有时需要从当前功能分支切出紧急修复分支：

首先确定要在哪个分支上修复 bug，假定需要在 master 分支（下图是 feature/xt）上修复，就从 master 创建新的临时分支

修复完成后，切换到 master 分支，并完成合并，最后删除这个新的临时分支

![截屏2020-12-29 下午1.33.55.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302474355-146fbe69-00c3-43a1-93a9-8a3351896923.png)

![截屏2020-12-29 下午1.25.16.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1656302473647-51bf9dae-f8ca-4748-85a3-c694b3bf34c5.png)



#### 查看当前 Git 分支是基于哪个分支创建的？

切换到要查询的分支下

使用命令：

git reflog --date=local | grep 要查询的分支名称

git reflog --date=local --all | grep 要查询的分支名称

按 shift+g ,切到最下面就可以查看命令输出的最后一行

注意：reflog 在远程分支上无效，只在本地分支才有效。

http://www.findme.wang/share/detail/id/542.html

另一种方法，一般新建分支是基于 master 分支创建的

使用命令：git reflog show `<childBranch>`

childBranch 是你新建的分支。fatherBranch 是它的父分支

查看最后一行：childBranch@{0}: branch: Created from fatherBranch

类似 develop@{1} branch: Created from origin/develop 的，是因为 fatherBranch 是远程分支，childBranch 是本地分支



#### 查看某个文件所有的改动日志

sourcetree 

左下角右击对应文件，选择选定项目的变更历史，就可以清楚呈现这个文件所有的改动记录，避免同事之间胡乱甩锅



#### 查看某个作者的提交记录

Sourcetree  分支上方 WORKSPACE 下 Search 

搜索右边下拉选择作者，输入作者名即可



#### 选择某次提交合并

git cherry-pick 可以理解为”挑拣”提交，

和 merge 合并一个分支的所有提交不同的是：它会获取某一个分支的单个提交，并作为一个新的提交引入到你当前分支上。

如下场景，以下有三条分支，

feature/cherry-pick1 和 feature/cherry-pick2 都是基于 master 检出的两条功能性分支，对应的分支 log 记录如下

![Image.webp](https://cdn.nlark.com/yuque/0/2022/webp/1614731/1656302471836-0240bc65-4ab1-48d1-b77b-5c5dfc8569ac.webp)

![Image [1].webp](https://cdn.nlark.com/yuque/0/2022/webp/1614731/1656302470893-60d273ea-74b5-42c6-9a56-461597085a81.webp)

master 分支的提交如下

![Image [2].webp](https://cdn.nlark.com/yuque/0/2022/webp/1614731/1656302471395-52e3cb33-49dd-4574-a44d-b25089c3355d.webp)

现在 master 只需要 feature/cherry-pick1 和 feature/cherry-pick2 有关 change 的修改，并不关心有关 fix 内容的修改。

此时就可以用 cherry-pick 指令了。

语法： git cherry-pick [commit-hash]

commit-hash 表示的是某次 commit 的 hash 值。

现在，依次执行以下两条指令 

git cherry-pick e0bb7f3、git cherry-pick c9a3101

过程中，如果出现冲突，解决冲突后 进行 git add ，接着执行 git cherry-pick --continue，最后，master 上的提交如下

![Image [3].webp](https://cdn.nlark.com/yuque/0/2022/webp/1614731/1656302471413-706128d9-6525-4ebd-9663-cfc79c624e02.webp)

https://juejin.cn/post/6974184935804534815

https://juejin.cn/post/7071780876501123085

Sourcetree 方式

Sourcetree 中使用 cherry-pick，直接在其它分支提交上右键遴选，确认即可

https://blog.csdn.net/u010234516/article/details/108582661



#### 多次提交合并为一次提交

**变基：基于某个分支的提交变化移动**

我们可能会由于各种各样的原因提交了许多临时的 commit，而这些 commit 拼接起来才是完整的任务。

那么我们为了避免太多的 commit 而造成版本控制的混乱，通常我们推荐将这些 commit 合并成一个。

特别注意，只能在自己使用的 feature 分支上进行 rebase 操作，不允许在公司分支上进行 rebase

那现在就开始动手吧！

git log 查看提交历史记录 ／ sourceTree里找到想要合并到的 commit 的父级 ，如 3a4226b 。

git log 历史记录是按照时间排序的，时间近的排在前面，最新的提交在最上面。

想要合并 1-3 条，有两个方法

1、从 HEAD 版本开始往过去数 3 个版本

```bash
git rebase -i HEAD~3
```

2、指定要合并的版本之前的版本号

```bash
git rebase -i 3a4226b
```

请注意 3a4226b 这个版本是不参与合并的（从上往下合并到的  3a4226b 这个commit 前一次提交——父级的版本），可以把它当做一个坐标

输入 i 进入 插入模式编辑，旧的 commit 在上面，新的 commit 在下面

修改（第2~最后一行）的第一个单词 pick 为  s

pick  : 表示保留该 commit，合并后的提交使用这个提交说明作为标题；

r : 表示保留该 commit ，但是修改该提交说明，合并后的提交使用这个修改的提交说明作为标题；

s : squash 命令的简写，代表合并提交中包含这个提交，将该提交和前一个提交合并，即这个 commit 会被合并到前一个 commit ；

f : 将该提交和前一个提交合并，但不要保留该提交说明；

d : 代表合并提交中排除这个提交。

按 Esc 退出插入模式

输入 :wq 保存退出

Git会压缩提交历史：

1、如果有冲突，需要修改，修改的时候要注意保留最新的历史，不然我们的修改就丢弃了。

修改以后要记得敲下面的命令：

```bash
git add .  

git rebase --continue  
```

如果你想放弃这次压缩的话，执行以下命令：

```bash
git rebase --abort  
```

2、如果没有冲突，或者冲突已经解决，输入 i 进入 插入模式编辑，则会出现如下的编辑窗口：

表示合并修改这几次提交的 commit 信息，一般都是把注释干掉, 然后写个新的

```bash
# This is a combination of 4 commits.  
# The first commit’s message is:  
添加注释1......
# The 2nd commit’s message is:  
添加注释2......
# The 3rd commit’s message is:  
添加注释3......
# Please enter the commit message for your changes. Lines starting # with ‘#’ will be ignored, and an empty message aborts the commit
```

改为

```bash
添加注释
# Please enter the commit message for your changes. Lines starting # with ‘#’ will be ignored, and an empty message aborts the commit
```

按 Esc 退出插入模式

输入 :wq 保存并退出

再次输入 git log 查看 commit 历史信息，你会发现 commit 已经合并了。

同步到远程 git 仓库自己的分支：git push -f 

**rebase 到第一次提交**

```sh
git rebase -i HEAD~4
```

但是这个 HEAD 是有个区间的。

如果提交了 4 次，使用 HEAD~4，是找不到最开始那个的 base，提示“无效的上游”。

```sh
git rebase -i --root
```

就可以回到根啦！

输入 i 进入 插入模式编辑

修改（第2~最后一行）的第一个单词 pick 为  s

按 Esc 退出插入模式

输入 :wq 保存退出

最后 git push -f

**举个例子**

合并前：

注意第三行和倒数第四行的提交，

以下操作要将第三行的提交 1693a6f 合并到第四行 3759b84 ，倒数第四行的提交 e57b0e6 合并到倒数第三行 17cb931

```sh
$ git log --oneline
8c8f3f4 update website
1693a6f update clear-logs.sh version
3759b84 update clear-logs.sh
fc36a2a add links
1d795e6 fix && update clear-logs.sh 0.0.2
9536dab add dingtalk script
3a51aaa fix shellcheck problem
2db6ad3 add clear logs scripts
e57b0e6 fix && add batch del
17cb931 fix && add batch del
cf7e875 add redis script
fe4bbcb Initial commit
```

编辑要合并版本

```sh
# 指定要合并版本号，cf7e875 不参与合并，进入 vi 编辑器
$ git rebase -i cf7e875 
pick 17cb931 fix && add batch del
pick e57b0e6 fix && add batch del
pick 2db6ad3 add clear logs scripts
pick 3a51aaa fix shellcheck problem
pick 9536dab add dingtalk script
pick 1d795e6 fix && update clear-logs.sh 0.0.2
pick fc36a2a add links
pick 3759b84 update clear-logs.sh
pick 1693a6f update clear-logs.sh version
pick 8c8f3f4 update website
```

```sh
# 把 e57b0e6 合并到 17cb931，不保留说明；把 1693a6f 合并到 3759b84
pick 17cb931 fix && add batch del
f e57b0e6 fix && add batch del
pick 2db6ad3 add clear logs scripts
pick 3a51aaa fix shellcheck problem
pick 9536dab add dingtalk script
pick 1d795e6 fix && update clear-logs.sh 0.0.2
pick fc36a2a add links
pick 3759b84 update clear-logs.sh
s 1693a6f update clear-logs.sh version
pick 8c8f3f4 update website
```

合并后：

```sh
$ git log --oneline
47e7751 update website
4c2316c update clear-logs.sh
73f082e add links
56adcf2 fix && update clear-logs.sh 0.0.2
ebf3786 add dingtalk script
6e81ea7 fix shellcheck problem
64ca58f add clear logs scripts
9327def fix && add batch del
cf7e875 add redis script
fe4bbcb Initial commit
```

**原则：最好是对本地分支的修改进行合并，**

**不建议对已推送至远程的提交进行 rebase 合并（需要保证只有你一个人在该分支进行开发，否则 rebase 可能会导致提交记录的丢失）**

**永远不要在公共分支上 rebase ，永远不要对别人的分支和公共分支 push --force ！**

**往公共分支上合代码的时候，使用 merge ！**

https://juejin.cn/post/6969101234338791432

https://juejin.cn/post/6891822489194889230

https://juejin.cn/post/7103560564466515981



#### 删除最后一次提交

```bash
git reset --hard HEAD^
git push -f 远程源名 分支名
```

#### 删除某次提交

命令行 rebase 方式

git log 查看所有提交，按空格查看之前的提交

查看想删除的那次 commit 的下面那次的 id （即前一次的 commit_id）

git rebase -i commit_id (前一次的 commit_id )

该命令会弹出一个 vim 编辑框，在顶部可以看到该次提交之后的所有提交 格式是:pick XXXXX(commit msg) 

按 i 进入编辑模式，然后找到想要删除的那次提交，将它前面的 pick 改为 drop  之后按 ESC 和 :wq 进行保存退出

git log 可以看到需要删除的提交已经消失

最后，

```bash
git push -f 远程源名 分支名
```

sourcetree revert 方式

对于需去除的 commit 提交。右击弹出弹窗，点击提交回滚，如果有冲突，即先解决冲突。没有冲突则直接提交推送。

#### 删除某个分支中的部分代码

如 develop 分支上有 a，b，c，d，e 五个提交，需要删除 c,d 两次提交

情景：比如你把某个功能已经合并到了你的开发分支，同时之后还提交了一些代码。但是这个功能突然被告知要下架，这时候如何处理？

首先到达节点 a, 从该处拉出一个新分支如 copy_develop，然后选择遴选 b , e 。

回到原来的 develop 分支，重置到a这次的提交。然后再把 copy_develop 分支的代码合并到 develop 分支即可。

#### 修改最后一次 commit

https://www.jianshu.com/p/882548efdc57

https://segmentfault.com/a/1190000022926064

```bash
git commit --amend
```

i 进入插入模式，将提交信息修改后

ESC + :wq 保存退出

```bash
git push origin -f master
```

master 分支不建议使用 -f 参数，因为团队开发很容易删除其他同事的提交。（可以在自己的分支上使用）

#### 修改某一次 commit

展示最后两条 commit，旧的 commit 在上面，新的 commit 在下面

```bash
git rebase -i HEAD~2
```

i 进入插入模式，把想更新的 commit 前面的 pick 改为 edit 即可

ESC + :wq 保存退出

```bash
git commit --amend
```

进入想更新的 commit，可以修改提交信息

移到最新一次提交：

```bash
git rebase --continue
```

git log 可以看到编辑的提交已经更新

最后，

```bash
git push -f 远程源名 分支名
```

#### Sourcetree 选择其它分支的某个提交进行合并

https://blog.csdn.net/u010234516/article/details/108582661

可使用 git 的 cherry-pick 命令 如

```shell
git cherry-pick 4db0729d
```

把那些要提交的节点选出来提交。

对于sourcetree 即是遴选功能。选择要被提交过来的节点，右击，选择遴选选项。

#### 本地项目第一次提交推送到远程（https 方式）

SoureTree Create 一个 git 仓库给本地项目

远程 gitee （gitee 为例）创建一个仓库（不需要初始化一个 README.md ，这会自动提交一次）

复制 git 地址，在 SourceTree 设置一个远程源，取名为 origin

远程仓库会提醒创建并提交一次 README ，可以不创建 （推荐），直接提交，然后推送项目选择分支即可。

如果初始化一个 README.md ，自动提交了一次

在本地项目写好  `README` 和 `.gitignore`文件，直接提交，然后推送项目选择分支即可。

如果远程仓库自动提交了一次 README ，本地项目没有 README 文件，

推送错误并显示本地项目比远程项目多一个提交，直接在 SourceTree 右键 Initial Commit ，点击变基即可推送。 

如果远程仓库自动提交了一次 README ，本地项目有 README 文件，复制一份 README 文件到桌面，

删除本地有的 README 文件后，从远程获取并检出跟踪主分支，

所有文件添加到暂存区并提交推送到远程，

如果本地项目推送到远程后，会形成两次 commit，需要

```bash
git rebase -i --root
```

i 进入插入模式，将最后一条提交记录前的 pick 换成 s 

ESC + :wq 退出

```bash
git log
```

查看提交记录两条是否变为一条 ，最后

```bash
git push -f origin master
```

在 SourceTree 上重新拉取 master 分支到本地，提交记录将更新为一条记录



#### 分支推送问题？

如果分支文件变动过多，可以强制删除自己源上有问题的远程分支，

重新拉取公司仓库对应的远程分支到本地，将公司仓库对应的远程分支推送到自己 fork 的仓库，

然后检出到该远程分支，本地分支重新跟踪到远程分支。



#### 出现 Git 无法追踪本地代码情况？

先试试刷新 Sourcetree，查看 gitignore，

如果还是有问题，而且只是该项目的问题，那就是该项目的 git 文件出现问题，需要重新建一个本地仓库，重新拉取代码（推倒重来）。

原因是：改了 gitignore 所忽略的文件，所以 git 会自动还原代码。

https://segmentfault.com/q/1010000012099936



#### Sourcetree 使用

https://www.cnblogs.com/Can-daydayup/p/13128633.html
https://cloud.tencent.com/developer/article/1650541
https://blog.csdn.net/weixin_43837354/article/details/105936140
https://juejin.cn/post/6880707661147406350?utm_source=tuicool&utm_medium=referral
https://juejin.cn/post/6844904083057278990?utm_source=tuicool&utm_medium=referral
https://juejin.cn/post/6844903639404773383?utm_source=tuicool&utm_medium=referral



#### Sourcetree 与工作流

https://www.yuque.com/shuimen-jl49h/ebptzz/nqty6k



#### Sourcetree 新建分支

https://www.cnblogs.com/-mrl/p/13852413.html
https://juejin.cn/post/6844903960109662221

新建分支时报错，可能是本地已有该分支

https://blog.csdn.net/weixin_42107987/article/details/104517763

新建分支，分支命名为 release/v1.0 ，即可生成下图形式

![20210410094508.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1656302469260-1c7b244a-d176-4121-9b80-2ac44add22e5.jpeg)



#### Sourcetree 合并分支

https://blog.csdn.net/mohoward/article/details/80415825
https://jingyan.baidu.com/article/48a4205719118ca924250404.html
https://jingyan.baidu.com/article/1612d500984546e20e1eeefc.html



#### SourceTree 推送错误或合并错误？

https://juejin.cn/post/6844904083057278990
https://juejin.cn/post/6844903639404773383
https://www.cnblogs.com/EchoHG/p/7690002.html

#### SourceTree 回退撤销提交

https://www.cnblogs.com/chen1997/p/11057910.html
https://jifengchao.github.io/2017/09/24/tool-SourceTree%E5%9B%9E%E9%80%80%E6%92%A4%E9%94%80commit.html
[https://wayou.github.io/2020/11/26/Git-%E5%9B%9E%E9%80%80%E5%B7%B2-push-%E5%88%B0%E8%BF%9C%E7%AB%AF%E7%9A%84%E6%8F%90%E4%BA%A4/](https://wayou.github.io/2020/11/26/Git-回退已-push-到远端的提交/)

#### SourceTree 打 tag 标签

https://www.cnblogs.com/-mrl/p/13859321.html



![2021-11-27_161536.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1656302469251-5bbd8bd2-72d0-4b29-a61c-bf621553dd0e.jpeg)



































































































































































































































