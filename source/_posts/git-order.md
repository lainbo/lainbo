---
title: 'Git命令集'
date: 2019-02-05 10:12:14
tags: [学习记录]
description: Git的各种命令。
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/Git-order.jpg)

## Git 常用命令
***
+ 初始化一个Git(本地)仓库 (只需初始一次)
``` js
git init
```
&nbsp;
+ 设置用户名和邮箱 (只需设置一次)
``` js
// 用户名：
git config --global user.name 你的用户名
// 邮箱：
git config --global user.email 你的邮箱
```
&nbsp;
+ 把工作区的文件添加到暂存区 (需按自己的需求添加N次)
``` js
git add 文件名,文件名····
git add * (把当前工作区所有文件添加到暂存区)
git add ./ (把当前工作区所有文件添加到暂存区)
```
&nbsp;
+ 把暂存区的文件提交，生成一个版本 (需按自己的需求提交N次)
``` js
git commit -m "说明文字"
```
&nbsp;
+ 查看用户名或邮箱等一系列设置 (可自行修改，按Q键退出)
``` js
git config --list
```
&nbsp;
+ 查看文件版本
``` js
git log (查看分支上都有哪些版本)
git log --oneline (查看分支上的版本，相对简洁)
```
&nbsp;
+ 查看文件状态
``` js
git status
```
&nbsp;
+ 查看文件版本id
``` js
git reflog
```
&nbsp;
+ 回退版本(一旦回退版本，工作区的代码也会相应改变)
``` js
git reset --hard HEAD^/commit_id
git reset --hard HEAD^ / HEAD~1 (表示回到上一个版本)
git reset --hard HEAD^^ / HEAD~2 (表示回到上上一个版本，依次类推)
git reset --hard 版本id (回到指定的版本，一般用这个)
```
&nbsp;
+ 回到未来版本(需通过git log查看版本id)
``` js
git reset --hard 版本id
```
&nbsp;
+ 撤回在暂存区的文件
``` js
git reset HEAD -- (撤回所有)
git reset HEAD -- 文件名 (撤回指定文件)
```
&nbsp;
+ 克隆远程仓库的代码到本地
``` js
git clone 远程仓库地址
```
**注：一般第一次用 git clone 远程仓库地址 进行拷贝，以后都用 git pull 进行拉取**
&nbsp;
+ 克隆远程仓库指定分支的代码到本地
``` js
git clone -b 分支名 远程仓库地址
```
&nbsp;
+ 从远程仓库拉取代码到本地(master为主分支)
``` js
git pull 远程仓库地址 master
```
**注：如果本地仓库与远程仓库同步后，只需输入 git pull 就可以直接进行拉取更新代码**
&nbsp;
+ 查看执行 git status 的结果的详细信息
``` js
git diff
```
&nbsp;
+ 推送本地代码到远程仓库(第一次同步时需输入密码)
``` js
git push 远程仓库地址 master
```
&nbsp;
简便方式：
+ 添加远程仓库:git remote add origin 远程仓库地址
+ 查看远程仓库：git remote
  如果显示 origin 则说明添加成功，以后再推送代码到远程仓库时，只需输入 git push origin master 就可以推送代码。
  当使用代码 git push -u origin master 时，表示把origin设置为默认主机，则再次推送代码时可简写为 git push
  &nbsp;
  **注：如果当前分支与多个主机存在追踪关系，则可以使用  -u  选项指定一个默认主机，这样后面就可以不加任何参数使用 git push**

+ 删除设置的origin(origin名称需根据你本地查询出来的名字进行删除)
``` js
// 查询：
git remote -v
// 删除：
git remote rm origin
```
&nbsp;

## Git 分支命令
+ 查看本地分支
``` js
git branch
```
&nbsp;

+ 查看远程分支
``` js
git branch -r
```
&nbsp;
+ 查看所有分支
``` js
git branch -a
```
&nbsp;
+ 创建本地分支
``` js
git branch 分支名
```
&nbsp;
+ 创建远程分支
``` js
git push --set-upstream origin 分支名
```
&nbsp;
+ 切换分支
``` js
git checkout 分支名
```
&nbsp;
+ 创建并切换分支
``` js
git checkout -b 分支名
```
&nbsp;
+ 合并分支(合并分支之前需要切换到master分支上,合并完需要push一下)
``` js
git merge 分支名
```
　　但是我们平常不用这个指令来合并分支，我们通常使用git merge --squash 分支名。
--squash选项的含义是：本地文件内容与不使用该选项的合并结果相同，但是不提交、不移动HEAD，因此需要一条额外的commit命令。其效果相当于将分支上的多个commit合并成一个，放在当前分支上，原来的commit历史则没有拿过来。
判断是否使用--squash选项最根本的标准是，待合并分支上的历史是否有意义。如果没有意义，就可以直接使用带--squash这个指令。
使用这个命令之后，我们需要commit一次，在提交的我们最好加上标识，然后再进行push

&nbsp;
+ 删除本地分支
``` js
git branch -d 分支名
```
&nbsp;
+ 删除远程仓库的分支
``` js
git push origin --delete 分支名
```
&nbsp;
+ 提交(commit)时不进行eslint校验(不建议使用)
``` js
git commit --no-verify -m "描述信息"
```
&nbsp;
+ 用来临时地保存一些还没有提交的工作，以便在分支上不需要提交未完成工作就可以清理工作目录
``` js
git stash
```
&nbsp;
+ 恢复未完成的工作状态
``` js
git stash pop
```
&nbsp;
>同一个项目，当你本地代码更改后，如果你的远程仓库的代码也更改了，这时候你要拉取远程仓库的代码，同时保证本地代码不被覆盖。
>这时候就可以操作步骤：git stash(保存当前的工作状态)，git pull(拉取远程仓库代码)，git stash pop(恢复之前的工作状态)



## 如何使用SSH来关联仓库
1. 检查你电脑上是否有SSH Key
```
ssh ls
```
2. 创建SSH Key (如果有的话跳过这一步)
```
ssh-keygen -t rsa -C "你的邮箱"
```
>这时候会显示<br>Generating public/private rsa key pair.<br>Enter file in which to save the key (/c/Users/16627/.ssh/id_rsa):<br>这是让你输入一个文件名，用于保存刚才生成的 SSH key 代码。为了避免麻烦，不用输入，直接敲3下回车，那么就会默认生成id_rsa和id_rsa.pub两个秘钥文件。

到这里你的秘钥就设置好了，你会收到这段代码提示
>Your identification has been saved in /c/Users/…/.ssh/id_rsa<br>Your public key has been saved in /c/Users/…/.ssh/id_rsa.pub

3. 添加SSH Key到GitHub/Gitee 在对应的位置添加公钥，记得是个人的公钥，不是部署的，部署的公钥只能pull和fetch，个人的才能提交代码上去

4. 添加的时候输入便于自己辨认的标题，下面的内容在之前生成公钥的时候会给你一个本地C盘之类的地址，如果有Everything之类的软件直接搜索“id_rsa.pub”，用记事本或者编译器之类的打开，将里面的内容粘贴到网页中

5. 测试一下该SSH key,输入
```
具体是哪一行取决于你用的是github还是码云

ssh -T git@github.com

ssh -T git@gitee.com
```
然后会提示你：
>The authenticity of host ‘github.com (13.229.188.59)’ can’t be established.
<br>RSA key fingerprint is SHA256:nThbg6kXUp…
<br>Are you sure you want to continue connecting (yes/no/[fingerprint])?

6. 直接输入yes
接下来就会提示你输入密码，如果上边设置ssh的时候，你没设置密码会提示你：

>Warning: Permanently added ‘github.com,192.30.255.112’ (RSA) to the list of known hosts.
<br>警告完了，如果你能看到如下提示，那你已经成功设置SSH密钥。
<br>Hi “用户名”! You’ve successfully authenticated, but GitHub does not provide shell access.

如果你看到 access denied，者表示拒绝访问，那么你就需要使用 https 去访问。
7. 接下来就可以

+ git init
+ git remote add origin xxx(注意这里是对应的SSH地址，不是HTTPS的地址)
+ git fetch ...

## 使用工具(2021-06-07)更新  
使用github desktop软件，上述的大部分操作都不需要人工完成，也能更快更好的使用git