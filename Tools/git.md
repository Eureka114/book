# Git 部分命令
## `status`
- `git status`是一个用于查看 Git 仓库当前状态的命令，可以查看在你上次提交之后是否有对文件进行再次修改。

	```bash
	$ git status
	On branch master
	
	Initial commit
	
	Changes to be committed:
	  (use "git rm --cached <file>..." to unstage)
	
	    new file:   README
	    new file:   hello.php
	```

	git status 命令会显示以下信息：

	- 当前分支的名称。

	- 当前分支与远程分支的关系（例如，是否是最新的）。

	- 未暂存的修改：显示已修改但尚未使用 `git add` 添加到暂存区的文件列表。

	- 未跟踪的文件：显示尚未纳入版本控制的新文件列表。（未使用`git commit -m"..."`命令）

		![](https://img2.imgtp.com/2024/05/29/fvXIqgWk.png)

	根据 git status 的输出，你可以判断当前工作目录中的文件状态，并采取适当的操作。

	通常我们使用 **-s** 参数来获得简短的输出结果：

	```bash
	$ git status -s
	AM README
	A  hello.php
	```

	**AM** 状态的意思是这个文件在我们将它添加到缓存之后又有改动。

## `diff`

查看、比较被`Git`管理的文件在 工作区、暂存区和版本库之间的差异，就需要用到 `git diff` 命令。

![](https://img2.imgtp.com/2024/05/29/vanJ3zHP.png)

下面讨论以下四个情景下文件的差异的比较：（这里，查看各种差异的方式主要是`git diff`的不同参数。）

### 工作区和暂存区

**`git diff` 命令，默认查看的就是 工作区 和 暂存区 之间文件的差异**。暂存区，就是已经添加到下一次的`commit`的清单而还没有进行`commit`操作的文件存储的区域。

> `git diff [--filename1 ...]` ：查看工作区与暂存区之间的文件（可以是指定的单个或多个文件；如不指定，默认是全部文件）差异。

### 工作区和版本库

使用`HEAD`查看最新版本库，也可以使用如`9fe238c`的指定版本号来查看和其之间的差异。

> `git diff [version] [--filename1 ...]` ：查看工作区与指定版本（如不指定，默认为`HEAD`；如果没有`HEAD`，则展示全部版本）之间的文件（可以是指定的单个或多个文件；如不指定，默认是全部文件）差异。

### 暂存区和版本库

使用`--staged `（或`--cached`）参数，可以用来查看暂存区中和指定提交之间的文件差异。

> `git diff --cached [version] [--filename1 ...]` ：查看暂存区与指定版本（如不指定，默认为`HEAD`；如果没有`HEAD`，则展示全部版本）之间的文件（可以是指定的单个或多个文件；如不指定，默认是全部文件）差异。

### 不同版本之间

> - `git diff version1 version2 [--filename1 ...]`：查看两个版本之间的文件（可以是指定的单个或多个文件；如不指定，默认是全部文件）差异。
> - `git diff version1 version2 --stat`：查看两个版本之间的文件差异的统计信息。
> - `git diff version1 version2 --shortstat`：查看两个版本之间的文件差异的简短统计信息。
> - `git diff version1 version2 [folder]`：查看两个版本之间的某个文件夹下的文件差异。
