# Git

## 创建版本库与⽂件操作

1. <font size=4>git init 将⽂件夹变成可管理的仓库</font>
2. <font size=4>git add</font>

![image-20240717185549503](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717185549503.png)

3. <font size=4>git commit -m</font>

![image-20240717185730945](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717185730945.png)

4. 查看状态

![image-20240717185853542](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717185853542.png)

5. <font size=4>git log --pretty=oneline</font>

![image-20240717190526889](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717190526889.png)

6. <font size=4>回退上⼀个版本</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240717190829684.png" alt="image-20240717190829684" style="zoom:150%;" />

7. <font size=4>根据reflog的提交id回到原来的版本</font>

![image-20240717191824251](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717191824251.png)

![image-20240717191850239](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717191850239.png)

8. <font size=4>撤销修改</font>

![image-20240717193720413](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717193720413.png)

9. <font size=4>修改⽂件以后add ，想回到修改前的状态(上⼀次add到暂存区的样⼦) 删除恢复也可以⽤这种⽅法，删除也是⼀种修改。</font>

- <font size=4>先⽤git restore --staged Vue.md将暂存区的⽂件从暂存区撤出，但不会更改⽂件</font>

  <font size=4>再⽤git restore Vue.md将在⼯作空间但是不在暂存区的⽂件撤销更改</font>

![image-20240717194610585](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717194610585.png)

- <font size=4>先⽤ git reset HEAD Vue.md</font>

  <font size=4>再⽤git checkout -- Vue.md将⼯作区的⽂件修改撤销</font>

![image-20240717200654765](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717200654765.png)

10. <font size=4>将⽂件add以后将⽂件删除</font>

- <font size=4>确实需要删除</font>

![image-20240717205034195](/Users/wangliang/Library/Application Support/typora-user-images/image-20240717205034195.png)

- <font size=4>误删恢复⽤9的⽅法</font>

## 远程仓库

1. <font size=4>将本地⼯作区与远程仓库关联(需要先配置SSH)</font> https://blog.csdn.net/weixin_42310154/article/details/118340458)

```
git remote add origin git@github.com:GItwangliangliang/Note.git
```

2. <font size=4>将本地内容推到远程仓库</font>

```
git push -u origin master
//推送时报错因为远程仓库和本地不⼀致，可能是远程仓库有README
https://blog.csdn.net/hzyhhhwc/article/details/125655397
git pull --rebase origin master
```

3. <font size=4>删除远程仓库（解除了本地和远程的绑定关系）</font>

<font size=4>先⽤ git remote -v 查看远程库信息</font>

![image-20240718094216050](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718094216050.png)

<font size=4>根据名字删除，⽐如删除 origin</font>

```
git remote rm origin
```

4. <font size=4>克隆仓库</font>

```
git clone git@github.com:michaelliao/gitskills.git
```



## 分支管理

### 分支示意

<font size=4>master 分⽀是⼀条线，Git⽤ master 指向最新的提交，再⽤ HEAD 指向 master ，就能确定当前分⽀，以及当前分⽀的提交点</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718095628851.png" alt="image-20240718095628851" style="zoom:50%;" />

<font size=4>当我们创建新的分⽀，例如 dev 时，Git新建了⼀个指针叫 dev ，指向 master 相同的提交，再把 HEAD 指向 dev ，

就表示当前分⽀在 dev 上</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718095718688.png" alt="image-20240718095718688" style="zoom:50%;" />

<font size=4>从现在开始，对⼯作区的修改和提交就是针对 dev 分⽀了，⽐如新提交⼀次后， dev 指针往前移动⼀步，⽽ master 指针不变</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718100047531.png" alt="image-20240718100047531" style="zoom: 50%;" />

<font size=4>我们在 dev 上的⼯作完成了，就可以把 dev 合并到 master 上。Git怎么合并呢？最简单的⽅法，就是直接把

master 指向 dev 的当前提交，就完成了合并</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718100119196.png" alt="image-20240718100119196" style="zoom:50%;" />

<font size=4>合并完分⽀后，甚⾄可以删除 dev 分⽀。删除 dev 分⽀就是把 dev 指针给删掉，删掉后，我们就剩下了⼀

条 master 分⽀</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718100209831.png" alt="image-20240718100209831" style="zoom:50%;" />

### 分支操作

> 查看分支：`git branch`
>
> 创建分支：`git branch <name>`
>
> 切换分支：`git checkout <name>`或者`git switch <name>`
>
> 创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`
>
> 合并某分支到当前分支：`git merge <name>`
>
> 删除分支：`git branch -d <name>`	

1. <font size=4>创建分支，切换分支</font>

```
git checkout -b dev
//git checkout命令加上-b参数表示创建并切换，相当于以下两条命令
git branch dev
git checkout dev
```

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718100734755.png" alt="image-20240718100734755" style="zoom:150%;" />

2.<font size=4>查看分支</font>

<img src="/Users/wangliang/Library/Application Support/typora-user-images/image-20240718101103364.png" alt="image-20240718101103364" style="zoom:150%;" />

3. <font size=4>对文件内容修改，add，commit</font>

![image-20240718102044611](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718102044611.png)

![image-20240718101605013](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718101605013.png)

4. <font size=4>切回main分支</font>

![image-20240718102243476](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718102243476.png)

5. <font size=4>合并分支，删除分支</font>

![image-20240718102425382](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718102425382.png)

### 解决冲突

1. <font size=4>创建并切换分支</font>

![image-20240718142838992](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718142838992.png)

2. <font size=4>在两个分支上对文件修改并提交</font>

![image-20240718143429506](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718143429506.png)

![image-20240718143605545](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718143605545.png)

3. <font size=4>合并分支，发生冲突</font>

![image-20240718144325086](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718144325086.png)

4. <font size=4>查看文件内容，修改</font>

![image-20240718144452011](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718144452011.png)

5. <font size=4>add commit</font>

![image-20240718144558979](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718144558979.png)

6. <font size=4>查看分支情况</font>

```
git log --graph --pretty=oneline --abbrev-commit
```

![image-20240718144653091](/Users/wangliang/Library/Application Support/typora-user-images/image-20240718144653091.png)

### 分支管理策略
