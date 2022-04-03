---
title: git命令
---


创建本地分支dev，并且和远程origin/dev分支关联:

```git
git checkout -b dev origin/dev
```



撤销对所有已修改但未提交的文件的修改，但不包括新增的文件

```git
git checkout .  
```



关联远程分支

```git
git branch --set-upstream-to=origin/remote_branch  your_branch
```



移除proxy配置

```git
git config --global --unset http.proxy  
```



查看global配置

```git
git config --global --list
```



删除远程分支

```git
git push origin --delete dev-kk
```

