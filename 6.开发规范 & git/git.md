# git

### 1. 原理

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.m0nuhrqi.webp)

### 2. git 常用命令

|               命令名称               |      作用      |
| :----------------------------------: | :------------: |
| git config --global user.name 用户名 |  设置用户签名  |
| git config --global user.email 邮箱  |  设置用户邮箱  |
|               git init               |  初始化本地库  |
|              git status              | 查看本地库状态 |
|            git add 文件名            |  添加到暂存区  |
|   git commit -m "日志信息" 文件名    |  提交到本地库  |
|              git reflog              |  查看历史记录  |
|       git reset -- hard 版本号       |    版本穿梭    |

> 设置用户签名和邮箱与登录github的账户没有任何关系，只是为了表示本人

> 初始化本地库会生成 .git 文件夹，将这个目录交给 git 管理