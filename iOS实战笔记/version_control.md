# 版本控制

###SVN
- 默认情况下每次对svn服务器进行一次修改，那么版本号就会自动加1
- revert 是返回到上次提交后版本的最原始状态，只可以返回没有提交的最原始状态，一旦提交，使用这个命令，就无法返回到之前的状态，这个时候需要使用merge命令
- 先删除 再自动出现再去ignore
-



###Git
- 版本号问题：采用散列 ，生成的值不一样，保证不会冲突，防止两个人都提交本地一样的版本号

- 7位版本号作用：用来做指定版本回退
 - git reset --hard 7位版本号

- 快捷键需要自己配置
- 多人开发中，需要先add。再commit，最后才可以push，只add就push是不行的，需要先add到暂缓区，然后commit到分支，才可以push到远程共享库，红色工作区，绿色暂缓区
- fetch first  超时
- .ignore文件只对工作区的文件有效，一旦add到暂缓区，这个ignore就无效了,**所以创建之前需要先给ignore文件添加忽略文件**


```objc
Git init/git  本地仓库
config --global user.name lb/git config --global user.email 735141021@qq.com
Git init –bare 共享库
初始化并配置信息
文件新添加或者删除后（右键删除）都是红色（rm命令删除是正常的），红色是在工作区，然后先add、，将其添加到暂缓区，然后再commit
```

- 写了一段时间的项目，需要加入版本控制
 - 进入项目文件夹
 - git init
 - git add . --all
---
- 之前只有在本地加入版本控制的项目，现在想加入到远程git仓库
 - 在远程比如github创建一个仓库，clone下来后，直接将远程仓库的config文件更换到本地仓库里面就可以了
 - .git文件要使用本地的，因为里记录了很多本地之前的提交记录


-  pod install —no-repo-update ,不更新所有的框架，只下载最新的我当前需要的框架

