项目中因为权限合并更新到sit后发现后端有问题，需要时间修复，前端只能回退到某个版本。但是这样导致新功能无法正常测试。一番搜索后发现可以drop到某一次的commit，把权限相关的功能回退。

方式有几种，分情况使用：

1. `git reset`

团队开发的时候git reset需要谨慎，因为回把之前的commit抛弃掉，导致记录丢失，个人开发无所谓。

```
# 回退到出问题之前的版本
git reset --hard 1d7444

#将某次commit的更改应用到当前版本(将出错 cmmit 之后别人提交的代码合并到当前正常代码分支上)
git cherry-pick 626335

#强制提交
git push origin HEAD --force
```



2. `git revert`

此种方式比较温和，能够完美的回退掉某一次commit

```
git revert 100047dcc
```

如果报错，可能是回退的commits包含合并记录，这时可以指定parent分支index，从1开始

```
git revert 100047dcc -m 1
```

然后git push



3. `git rebase`

   此种方式未验证

   ```
   1，首先git log查看提交记录，找到出错的前一笔提交的commit_id
   
   2，用命令git rebase -i commit_id ,查找提交记录
   
   3，将出错那笔提交的pick改为drop
   
   4，Esc，:wq
   
   ```

   

清除merge记录

git rebase