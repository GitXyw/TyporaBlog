# git 常用的操作命令

- `git commit -a -m "提交信息"`

​				`git add`添加和`git commit`提交指令合并

- `git log --oneline --decorate --graph --all`

  显示每次提交的信息，log信息太长了不想继续看可按`q`退出  

- `git show 12se322:file.project > showfile.project`

  提取`12se322`提交中的“file.project”，在工作空间中保存为“showfile.project”。这条指令对于非字符型的文件如PLC程序等二进制文件的分支合并非常有用。即可以拉取不同分支的文件，然后再手动对比合并。  

- `git clean -f`

  清除没有添加到暂存区的文件，即没有`git add`的文件。对于不能自动比对合并的二进制文件，通过上一条指令拉取出不同分支后，手动合并后，就可以用本条指令把这些拉取出的临时文件清除。