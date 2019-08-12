# Git-study

> 工作区（写代码） 
>
> -- git add -->
>
> 暂存区（临时存储） 
>
> -- git commit -->
>
> 本地库（历史版本）

## 基本操作

| 操作                              | 命令                                    |
| --------------------------------- | --------------------------------------- |
| 状态查看                          | git status                              |
| 将工作区的“新建/修改”添加至暂存区 | git add [file name]                     |
| 将暂存区的内容提交到本地库        | git commit -m "commit msg" [file name]  |
| 查看历史记录（最完整形式）        | git log(多屏：空格向下；b向上；q退出；) |
| 查看历史记录（一行显示）          | git log --pretty=oneline                |
| 查看历史记录（一行最简洁显示）    | git log --oneline                       |
| 查看历史记录（显示移动版本步数）  | git reflog                              |
| 移动hard指针                      | git reset --hard [局部索引值]           |
| hard指针回退一步                  | git reset --hard HEAD^                  |
| hard指针回退N步                   | git reset --hard HEAD~N                 |

## 概念

### 1. 本地库初始化：git init

### 2. 设置签名：用户名与email地址

- 项目级别/仓库级别：仅在当前本地库范围内有效
  git config user.name tom_pro

  git config user.email goodMorning_pro@atguigu.com

- 系统用户级别：登录当前操作系统的用户范围
  git config -global user.name tom_glb

  git config -global user.email goodMorning_glb@atguigu.com

> .git/config里可看配置 

### 3. git add <file>

### 4. reset命令的三个参数对比

* --soft

  仅在本地库移动HEAD指针

* --mixed

  在本地库移动HEAD指针，重置暂存区

* --hard

  在本地库移动HEAD指针，重置暂存区，重置工作区

### 5. 删除文件并找回

* 前提：删除前，文件存在时的状态提交到了本地库。（曾存在于本地库）
* 操作：git reset --hard [指针位置]
  1. 删除操作已经提交到本地库：指针位置指向历史记录
  2. 删除操作尚未提交到本地库：指针位置使用HEAD

### 6. 分支

#### 分支操作

| 分支操作 | 命令                           |
| -------- | ------------------------------ |
| 创建分支 | git branch[branch_name]        |
| 查看分支 | git branch -v                  |
| 切换分支 | git checkout [branch_name]     |
| 合并分支 | git merge [branch_new_content] |

#### 解决冲突

* 编辑冲突文件，删除特殊符号

* 修改并:wq

* git add [冲突文件名]

* git commit -m "日志消息"

  > 不带具体文件名

### 7. git本地库与github远程库

| 操作                      | 命令                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 将git与github新建的库绑定 | git remote add origin https://github.com/LinWwwwwwww/huashan.git |
| 将master分支push到远程库  | git push -u origin master                                    |
| 将远程库克隆到本地        | git clone https://github.com/LinWwwwwwww/huashan.git         |

> clone操作：完整库下载到本地；与远程库绑定；初始化本地库。