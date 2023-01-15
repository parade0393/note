1. HEAD指向某一个分支的最新节点
2. 修改历史版本的话，就以某一个历史版本为起点，创建一个新的分支
3. fetch 又fork的话，是从原始仓库抓取最新的信息，先fetch  再pull

常用命令：

1. 查看所有分支

   git branch -a ---查询结果中带有remotes/origin的是远程分支

2. 删除远程分支

   git push origin --delete 分支名称(即remotes/origin之后的名称)
   
3. 重命名远程分支名称

   1. 删除远程分支