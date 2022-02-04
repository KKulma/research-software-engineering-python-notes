This command shows us the differences between the current state of our repository and the most recently saved version:

```shell
$ git diff
```

We can be specific about what file we want to see changes for:
(will show the difference between the file as it is now and the most recent version)
```shell
diff --git a/results/dracula.png b/results/dracula.png
index c1f62fd..57a7b70 100644
Binary files a/results/dracula.png and 
b/results/dracula.png differ
```


`git diff 582f7f6`, on the other hand, shows the difference between the current state and the commit referenced by the short identifier:
`$ git diff HEAD~1..HEAD~2` - This shorthand is read “HEAD minus one,” and gives us the difference to the previous saved version. git diff HEAD~2 goes back two revisions and so on

### List all the remote URLs:

```shell
$ git remote -v
origin  https://github.com/amira-khan/zipf.git (fetch)
origin  https://github.com/amira-khan/zipf.git (push)
```


### Show commit history with changes

```shell
$ git log -p

commit ee8684ca123e1e829fc995d672e3d7e4b00f2610
(HEAD -> master, origin/master)
Author: Amira Khan <amira@zipf.org>
Date:   Sat Dec 19 09:52:04 2020 -0800

    Update dracula plot

diff --git a/results/dracula.png b/results/dracula.png
index c1f62fd..57a7b70 100644
Binary files a/results/dracula.png and 
b/results/dracula.png differ
...
```
`
If we want to see the changes made in a particular commit, we can use git show with an identifier and a filename:

```shell
$ git show HEAD~1 bin/plotcounts.py
```

```shell
commit 582f7f6f536d520b1328c04c9d41e24b54170656
Author: Amira Khan <amira@zipf.org>
Date:   Sat Dec 19 09:37:25 2020 -0800

    Plot frequency against rank on log-log axes

diff --git a/bin/plotcounts.py b/bin/plotcounts.py
index f274473..c4c5b5a 100644
--- a/bin/plotcounts.py
+++ b/bin/plotcounts.py
@@ -13,7 +13,7 @@ def main(args):
                                            method='max')
     df['inverse_rank'] = 1 / df['rank']
     ax = df.plot.scatter(x='word_frequency',
-                         y='inverse_rank',
+                         y='rank', loglog=True,
                          figsize=[12, 6],
                          grid=True,
                          xlim=args.xlim)
```


### Restoring old versions of files

Because git restore is designed to restore working files, we’ll need to use git checkout to revert to earlier versions of files. We can use a specific commit identifier rather than HEAD to go back as far as we want:

```shell
$ git checkout 851d590 bin/plotcounts.py
```


#### Restore from the staging area:

```shell
$ git restore --staged bin/plotcounts.py
```