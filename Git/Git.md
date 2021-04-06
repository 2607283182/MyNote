git rm --cached xxx ：从暂存区里移除文件，但是不删除工作区的文件。即取消跟踪这个文件。

git reset HEAD -- xxx：将 xxx 文件，最后一次提交（HEAD指针的位置）的版本，放入暂存区。

都可以用来取消当前改动的暂存，但 git rm --cached xxx 会取消跟踪文件，而 git reset HEAD -- xxx 不会。

git diff  暂存区和当前工作目录文件区别
git diff --staged 暂存区和工作目录原始文件区别

 git checkout -- <file>  恢复文件到原始工作目录一样 = svn revert