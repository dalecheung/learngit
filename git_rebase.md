#### git merge 与 git rebase

---

```
- $ cd hellogit	#创建hellogit 目录
- $ git init   #初始化git项目
- $ vi readme #新建readme文件，添加内容
- $ git add .  #提交内容
- $ git commit -m 'init project c1' #git系统默认创建一个master分支
```

**创建dev分支，并添加内容**

```
- $ git switch -c dev
- $ vi readme   #在原来基础上添加内容
- $ git add .
- $ git commit -m 'add hello world c2'
```

**切回master分支**

```
- $ git switch master
- $ vi readme  #第2行增加'hello world from master'
- $ git add .
- $ git commit -m 'add hello world c3'
- $ vi hello.py  #新增 hello.py 文件
- $ git add .
- $ git commit -m 'add hello.py c4'
```

**切回dev分支**

```
- $ git switch dev
- $ vi helloworld.py  #增加 helloworld.py 文件
- git add .
- git commit -m 'add helloworld.py c5'
```

master 分支节点链表指向为：c1 <--c3<--c4

dev分支的节点链表指向为：c1<--c2<--c5

master分支和dev分支祖先为c1，假定在master分支上做git merge dev合并，得到的提交历史为：
c1<--c2<--c3<--c4<--c5<--c6（c1、c4、c5做了一次三方合并发现冲突，手工处理完毕后git add/commit增加了提交节点c6）
采用git merge dev处理提交log是按照时间戳先后顺序的。



**假定采用的是git rebase处理过程为：**

```
$ git switch dev
$ git rebase master # 将dev上的c2、c5在master分支上做一次衍合处理
		# git提示出现了代码冲突，此处为之前埋下的冲突点，处理完毕后
$ git add readme # 添加冲突处理后的文件
$ git rebase --continue # 加上--continue参数让rebase继续处理
```

此处处理后的节点为：

c1 c3 c4 c2 c5 # 此处不是按照时间顺序处理的
综合表现，git rebase可以得到一个更加简洁的提交历史，无需多了c6。

```
$ git log --graph --pretty=oneline --abbrev-commit
* 9d2d057 (HEAD -> dev) add hellowlrld.py c5
* 2c65522 add hello world c2
* c15c806 (master) add hello.py c4
* c5b1d70 add hello world c3
* e59256d init project c1
```

**之后，切回master分支，并合并dev分支：**

```
$ git switch master
$ git merge dev  # git会智能采用 fast forward处理。
```

**此时的log历史显示为：**

```
$ git log --graph --pretty=oneline --abbrev-commit
* 9d2d057 (HEAD -> master, dev) add hellowlrld.py c5
* 2c65522 add hello world c2
* c15c806 add hello.py c4
* c5b1d70 add hello world c3
* e59256d init project c1
```

**git rebase过程相比较git merge合并整合得到的结果没有任何区别，但是通过git rebase衍合能产生一个更为整洁的提交历史。
如果观察一个衍合过的分支的历史提交记录，看起来会更清楚：仿佛所有修改都是在一根线上先后完成的，尽管实际上它们原来是同时并行发生的。**