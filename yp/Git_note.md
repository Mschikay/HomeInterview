---
title: Git命令
date: 2018-03-18 16:42:30
tags: Git
categories: Git命令
---

#Git命令

0. 验证Git是否成功连接github

	ssh -T git@github.com
1. 添加远程仓库

	git remote add [name] [url]
2. 查看当前远程仓库

	git remote -v
3. 从远程仓库抓取数据

	git fetch [remote_nme]
4. 推送数据到远程仓库

	git push [remote_name] master
5. 查看远程仓库信息

	git remote show [remote-name]
6. 远程仓库删除和重命名

	git remote rename [old_remote_name] [new_remote_name}
	git remote rm [remote_name]
7. 
