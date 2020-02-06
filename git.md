**git笔记整理：**

**一、本地git操作：**

**1.查看工作区、stage、版本库之间差别：**

git diff是查看工作区和暂存区的差别
git diff --cached 是查看暂存区和仓库（上一次提交）的差别
git ls-files 查看暂存区有什么文件

**2.版本之间的回退和切换(仓库中):**
git reset --hard HEAD^^第一个^表示转义,回退到前一个版本
git reset --hard 1094a 回退或者前进到某一个版本号
也可用git reflog查看版本号 以便回到某一个版本

**3.写错了需要回退（工作区或stage中）：**

**1).若在工作区写错了或者删除了还没加入暂存区要回退：**
git checkout readme.txt 把工作区的修改回退到最近一次git add或者git commit的内容，对于git commit还有理解就是commit了之后暂存区和版本库内容一致,所以最近一次git add或者git commit可以统一理解成回退到暂存区
**2).若写错了或者删除了并且已经加入了暂存区要回退：**
git reset HAED readme.txt先将仓库中文件搬到暂存区
git checkout readme.txt 再把暂存区内容搬到工作区

***可以记为：checkout是从暂存回到工作区、reset是从版本库回到暂存区***

**4.要删除仓库中该文件:**
git rm test.txt 暂存区和仓库是一致的，要先在暂存区中删除
git commit -m "delete file test.txt" 再将暂存区内容commit

1.如果你用的del删除文件，那就相当于只删除了工作区的文件，如果想要恢复直接用git checkout -- <file>就可以 
2.如果你用的是git rm删除文件，那就相当于不仅删除了文件，而且还添加到了暂存区，需要先git reset HEAD <file>，然后再git checkout -- <file> 3.如果你想彻底把版本库的删除掉，先git rm，再git commit 就ok了

**二、分支管理：**
**1.已有本地库时：**
0).首先在github.com上新建一个repository
1).在本地learngit仓库下运行命令：
git remote add origin git@github.com:LIne0o0/gitLearning.git
该命令将本地仓库和远程一个仓库关联起来
2).再将本地库所有内容推送到远程库上：
git push -u origin master 
git push命令将当前master分支推送到远程，由于远程库一开始为空，所以加-u参数，此步命令不仅做了推送，还将本地master分支和远程master分支关联起来，可以简化命令。
3).往后再需要推送更新时：
git push origin master
4).如果是本地一个新分支 想要推到远程 但是远程还没有该分支时，需要：
git push --set-upstream origin +新分支名
5).注意在某个分支下的git push操作只对该分支有用，其他分支不会被push上去，这样也比较合理。

**2.要把远程的仓库拉到本地：**
git clone git@github.com:Line0o0/gitskills.git
或者git clone+https格式

**3.创建新分支：**
git checkout -b dev 其中-b参数是必要的，表示创建并切换。
相当于以下两条命令：
git branch dev
git checkout dev切换分支

**4.合并分支：**

git merge dev 是将指定分支合并到当前分支 如果可能，git会有Fast forward模式 但是这种模式看不出来做过合并
git merge --no-ff -m "XXXXXX" dev 
--no-ff 禁用Fast forward模式，git会在merge时生成一个新的commit 那么也就可以加上说明信息，合并后的历史有分支，能看出来曾经做过合并。
git log --graph --pretty=oneline --abbrev-commit查看分支历史图解
其中，--abbrev-commit仅显示SHA-1的前几个字符，而非所有的40个字符

**5.删除分支：**

git branch -d dev 删除本地分支dev
git push origin --delete dev 删除远程分支dev（单执行该命令本地的dev还会在，需用前一条删除本地分支）

**6.切换分支前保存当前工作区：**

stash理解：
1).首先所有文件是共享一个工作区的，没有stashed你根本换不了分支去做其他工作（比如修bug）。
2).git stash之前新文件（没有被add过的）需不需要做先add？好像都不影响，但最好还是add吧
使用git stash list可以查看最近的stash
git stash pop是恢复的同时把stash删了
git stash apply可以恢复到最近一次stash 但需要再用git stash drop stash@{}来删除
git stash apply stash@{}指定恢复哪一个stash

修复一个bug后，将更改应用到另一个派生出来的分支：
1.首先切换到想改的分支
2.git cherryout+commitID 将其他分支做的更改应用到本分支，而不用整个分支merge过来。但需要注意：应用更改其实没那么智能，反而是很暴力的。是将改了的那个文件内容整个搬过来，即使两个分支的同一文件内容可能并不兼容，也是暴力复制全部过来。

**7.对比两个命令：**
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name
如果是本地一个新分支 想要推到远程 但是远程还没有该分支时，需要：
git push --set-upstream origin +新分支名
其中：--set-upstream参数表示建立本地分支和远程分支的匹配关系

**8.标签操作：**

tag相当于commit的一个别名，因为commit ID不好记，但是tag好记
git tag v1.0 默认是给最新的commit打上tag
git tag v0.9 +commit ID给以前的commit补上tag 
git log --pretty=oneline --abbrev-commit查看以往commit ID
git tag查看所有标签
git show +tag名
git tag -a v0.1 -m "version 0.1 released" 1094adb创建带有说明的标签
其中-a表示添加别名，-m表示添加注释
git tag -d v1.0 删除标签
创建的标签只存储在本地不会自动推送到远端，要推送某个标签到远端：
git push origin +tag名
推送所有标签：git push origin --tags
要删除远程 git push origin ：refs/tags/tag名 删除和推送都是push，这里看起来很奇怪，但是理解是：冒号在这里，前面为空，即将一个“空”的标签推送给对应的标签，即删除