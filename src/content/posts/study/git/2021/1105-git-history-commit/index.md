---
title: "如何用 Git 命令修改历史提交"
date: 2021-11-05T16:37:27+08:00
slug: how-to-amend-git-history-commit
categories: study / git
---

在实际工程中，有时我们在以往的提交中忘记提交部分代码，当想起来的时候已经提交过很多笔了。于是想要把当前的更改提交到历史 commit 中去。

假设当前我们提交了三次：

```shell
$ git log
commit 0e162000e27ba998d5a92cc04b489e3d3ec0e30d (HEAD -> master)
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:46:08 2021 +0800

    3rd commit

commit f5f470406634bb255e4f19bf62780868afeed32d
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:45:21 2021 +0800

    2nd commit

commit cf8c580d322e459fa1acf4ef1e6d08163aeb1a21
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:41:30 2021 +0800

    1st commit

```

这几次提交就只涉及一个文件 `1.txt`

```shell
$ cat 1.txt
11111111
22222222
33333333
```

其中第一行为第一次提交，第二行为第二次提交，第三行为第三次提交。

此时如果想要将第二次提交增加一个新文件 `2.txt`，该怎么操作呢？

## 步骤

1. 首先新建 `2.txt`

```shell
$ cat 2.txt
22222222
```

2. 将当前的更改 stash

```shell
$ git stash
Saved working directory and index state WIP on master: 0e16200 3rd commit
```

3. 根据之前 `git log` 打印的 commit 信息确定将要更改的 commit hash 值。然后使用 `git rebase -i` （没错，这相当于一次 rebase）

```shell
$ git rebase -i f5f470406634bb255e4f19bf62780868afeed32d^
```

> ⚠️ 注意：这里输入的提交 hash 值后需要加上 `^`[^1]，代表这次 commit 的父提交。或者：
>
> ```shell
> $ git rebase -i cf8c580d322e459fa1acf4ef1e6d08163aeb1a21
> ```
>
> 或
>
> ```shell
> $ git rebase -i HEAD~2
> ```

> 如果此时需要更改第一次提交，因为第一次提交没有父提交，所以应该输入命令
>
> ```shell
> $ git rebase -i --root
> ```

4. 此时会进入 vim，编辑对于这些 commit 需要执行怎样的操作

```
pick f5f4704 2nd commit
pick 0e16200 3rd commit

# Rebase cf8c580..0e16200 onto cf8c580 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

我们当前需要变更第二次 commit，所以应该把第一行的 `pick` 改为 `edit`，然后 `:wq` 保存。

5. 此时进入这样的画面

```
Stopped at f5f4704...  2nd commit
You can amend the commit now, with

  git commit --amend 

Once you are satisfied with your changes, run

  git rebase --continue

```

这时可以将我们 stash 后的更改 pop 出来

```shell
$ git stash pop
interactive rebase in progress; onto cf8c580
Last command done (1 command done):
   edit f5f4704 2nd commit
Next command to do (1 remaining command):
   pick 0e16200 3rd commit
  (use "git rebase --edit-todo" to view and edit)
You are currently editing a commit while rebasing branch 'master' on 'cf8c580'.
  (use "git commit --amend" to amend the current commit)
  (use "git rebase --continue" once you are satisfied with your changes)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   2.txt

Dropped refs/stash@{0} (15fa455ffa846f31167b311c3b7b9314f6c944db)
```

6. 提交

```shell
$ git add .
$ git commit --amend
```

并确认提交信息。

7. 继续进行 rebase

```shell
$ git rebase --continue
Successfully rebased and updated refs/heads/master.
```

结束。

## 验证

此时我们看到第二次和第三次的提交的 hash 值都被修改了

```shell
$ git log
commit 9522b421544a86b1fc7be32bac18e889fb57b096 (HEAD -> master)
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:46:08 2021 +0800

    3rd commit

commit 903242f62a479417f1b3d759b0a64280ea43287b
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:45:21 2021 +0800

    2nd commit

commit cf8c580d322e459fa1acf4ef1e6d08163aeb1a21
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:41:30 2021 +0800

    1st commit
```

查看第二次提交：

```shell
$ git show HEAD^
commit 903242f62a479417f1b3d759b0a64280ea43287b
Author: sidneysliu <sidneysliu@tencent.com>
Date:   Fri Nov 5 13:45:21 2021 +0800

    2nd commit

diff --git a/1.txt b/1.txt
index ca028fb..71d9a24 100644
--- a/1.txt
+++ b/1.txt
@@ -1 +1,2 @@
 11111111
+22222222
diff --git a/2.txt b/2.txt
new file mode 100644
index 0000000..75a1dd2
--- /dev/null
+++ b/2.txt
@@ -0,0 +1 @@
+22222222
```



[^1]: https://segmentfault.com/a/1190000022506884

