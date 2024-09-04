# git

### 1. 原理

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.m0nuhrqi.webp)

### 2. git 常用命令

|                         命令名称                         |                  作用                   |
| :------------------------------------------------------: | :-------------------------------------: |
|           git config --global user.name 用户名           |              设置用户签名               |
|           git config --global user.email 邮箱            |              设置用户邮箱               |
|            <font color="red">git init</font>             |  <font color="red">初始化本地库</font>  |
|           <font color="red">git status</font>            | <font color="red">查看本地库状态</font> |
|         <font color="red">git add 文件名</font>          |  <font color="red">添加到暂存区</font>  |
| <font color="red">git commit -m "日志信息" 文件名</font> |  <font color="red">提交到本地库</font>  |
|           <font color="red">git reflog</font>            |  <font color="red">查看历史记录</font>  |
|     <font color="red">git reset --hard 版本号</font>     |    <font color="red">版本穿梭</font>    |

> 设置用户签名和邮箱与登录github的账户没有任何关系，只是为了表示本人

> 初始化本地库会生成 .git 文件夹，将这个目录交给 git 管理

> 输入 git status 会显示出三行日志
>
> On branch main // 表示在那个分支
>
> Your branch is up to date with 'origin/main'. // 更新状态
>
> nothing to commit, working tree clean // 缓存区很干净

> git reset --hard 版本号 实现的版本穿梭是改变本地工作区



|    **命令名称**     |           **作用**           |
| :-----------------: | :--------------------------: |
|  git branch 分支名  |           创建分支           |
|    git branch -v    |           查看分支           |
| git checkout 分支名 |           切换分支           |
|  git merge 分支名   | 把指定的分支合并到当前分支上 |















