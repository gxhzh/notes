# Git 操作笔记

```text
Git 操作
1. git branch
   1.1 git branch
   1.2 git branch --show-current
   1.3 git branch -a
   1.4 git branch -r
2. git status
   2.1 git status
   2.2 git status --short
   2.3 git status -s
   2.4 git status 和 git branch 的关系
3. Git 的三个常见区域
   3.1 工作区 working tree
   3.2 暂存区 staging area / index
   3.3 本地仓库 repository
   3.4 怎么在这些区域之间切换
4. git status --short 两列状态
   4.1  M file
   4.2 M  file
   4.3 MM file
   4.4 A  file
   4.5 ?? file
   4.6  D file
   4.7 D  file
   4.8 冲突状态
5. Git 跟踪
   5.1 什么是 Git 跟踪
   5.2 未跟踪文件是什么
   5.3 怎么触发跟踪
   5.4 跟踪之后会怎么样
   5.5 .gitignore 和未跟踪文件
   5.6 已经跟踪的文件，后面不想继续跟踪怎么办
6. git diff
   6.1 git diff
   6.2 git diff --cached
   6.3 git diff --stat
   6.4 git diff --cached --stat
   6.5 多个 -- 参数是什么意思
7. git add
   7.1 git add file
   7.2 git add .
   7.3 git add -A
   7.4 git add -u
   7.5 git add -p
   7.6 git add --dry-run
   7.7 git add --intent-to-add
   7.8 新文件 add 后为什么是 A，不是 M
8. git commit
   8.1 git commit
   8.2 git commit -m
   8.3 git commit --amend
   8.4 git commit --amend --no-edit
   8.5 amend 什么时候要小心
9. git log
   9.1 git log
   9.2 git log --oneline
   9.3 git log --graph --oneline --all
   9.4 git log -n
10. git show
   10.1 git show
   10.2 git show commit-id
   10.3 git show --stat commit-id
11. git push
   11.1 git push
   11.2 git push origin branch-name
   11.3 git push -u origin branch-name
   11.4 git push --force-with-lease
   11.5 push 前的日常检查
```

## Git 操作

### 1. git branch

`git branch` 主要用于查看和管理分支。

#### 1.1 git branch

作用：

```bash
git branch
```

列出本地所有分支，并用 `*` 标记当前所在分支。

示例：

```text
  dev
* main
  feature/login
```

这表示当前在 `main` 分支。

#### 1.2 git branch --show-current

作用：

```bash
git branch --show-current
```

显示当前所在分支的名字。

例如当前在 `main` 分支，输出就是：

```text
main
```

注意：正确写法是 `--show-current`，不是 `--show -current`。

这里的 `--show-current` 是一个“长选项”。

命令行参数一般有两类：

```bash
-v
--verbose
```

其中：

- `-v` 是短选项，通常只有一个字母。
- `--verbose` 是长选项，通常是完整单词，可读性更好。

所以这里的 `--` 不是单独的分隔符，而是长选项名称的一部分。

补充：在 Git 和很多命令行工具里，单独的 `--` 也可以作为“选项结束标记”，用于区分参数和文件路径。例如：

```bash
git checkout -- file.txt
```

这里的 `--` 表示后面的 `file.txt` 是文件路径，不是命令选项。

#### 1.3 git branch -a

作用：

```bash
git branch -a
```

列出本地分支和远程分支。

#### 1.4 git branch -r

作用：

```bash
git branch -r
```

只列出远程分支。

### 2. git status

`git status` 主要用于查看当前 Git 仓库的工作区状态。

#### 2.1 git status

作用：

```bash
git status
```

用于查看当前 Git 仓库的工作区状态。

它会告诉你几类重要信息：

- 当前在哪个分支。
- 当前分支和远程分支是否同步。
- 哪些文件被修改了但还没有暂存。
- 哪些文件已经暂存，准备提交。
- 哪些文件是新文件，但还没有被 Git 跟踪。
- 有没有冲突、rebase、merge 等特殊状态。

常见输出示例：

```text
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  modified:   src/main.tsx

Untracked files:
  getStart/git-操作笔记.md

no changes added to commit
```

这表示：

- 当前在 `main` 分支。
- 当前分支和远程 `origin/main` 同步。
- `src/main.tsx` 被修改了，但还没有 `git add`。
- `getStart/git-操作笔记.md` 是新文件，Git 还没跟踪。
- 当前没有任何文件进入暂存区，所以还不能提交这些改动。

如果工作区很干净，可能会看到：

```text
On branch main
nothing to commit, working tree clean
```

这表示当前没有未提交的改动。

#### 2.2 git status --short

作用：

```bash
git status --short
```

用于以更简洁的格式查看工作区状态。

示例：

```text
 M src/main.tsx
?? getStart/git-操作笔记.md
A  src/new-file.ts
M  package.json
```

每一行前面的两列状态很重要：

```text
XY 文件路径
```

其中：

- 第一列 `X` 表示暂存区状态。
- 第二列 `Y` 表示工作区状态。

常见状态：

```text
?? file
```

新文件，尚未被 Git 跟踪。

```text
 M file
```

文件被修改了，但还没有暂存。

```text
M  file
```

文件已修改，并且已经暂存。

```text
A  file
```

新文件已经被 `git add` 暂存。

```text
D  file
```

文件删除已经被暂存。

```text
 D file
```

文件在工作区被删除，但还没有暂存。

日常使用建议：

- 想看详细说明，用 `git status`。
- 想快速扫一眼有哪些文件变了，用 `git status --short` 或 `git status -s`。

#### 2.3 git status -s

作用：

```bash
git status -s
```

`git status -s` 是 `git status --short` 的简写，作用相同。

#### 2.4 `git status` 和 `git branch` 的关系

`git status` 和 `git branch` 有一点功能重叠，但关注点不同。

`git branch` 主要关注“分支”：

- 当前有哪些本地分支。
- 当前在哪个分支。
- 是否查看远程分支。

`git status` 主要关注“工作区状态”：

- 当前在哪个分支。
- 当前分支和远程分支是否同步。
- 哪些文件改了。
- 哪些文件已暂存。
- 哪些文件未跟踪。
- 当前是否有 merge/rebase 冲突。

所以：

- 只想知道当前分支名：用 `git branch --show-current`。
- 想看本地有哪些分支：用 `git branch`。
- 想知道当前仓库有没有改动、改动是否暂存：用 `git status`。

### 3. Git 的三个常见区域

理解 `git status --short` 前，先理解三个区域：

```text
工作区 working tree
  -> git add
暂存区 staging area / index
  -> git commit
本地仓库 repository
```

#### 3.1 工作区 working tree

工作区就是你电脑文件夹里实际看到和编辑的文件。

比如你用 IDE 修改了 `src/main.tsx`，这个修改首先发生在工作区。

此时执行：

```bash
git status --short
```

可能看到：

```text
 M src/main.tsx
```

这表示文件在工作区改了，但还没进入暂存区。

#### 3.2 暂存区 staging area / index

暂存区可以理解成“下一次提交的候选清单”。

你执行：

```bash
git add src/main.tsx
```

就是把 `src/main.tsx` 当前版本放进暂存区。

此时可能看到：

```text
M  src/main.tsx
```

这表示修改已经暂存，下一次 `git commit` 会提交它。

#### 3.3 本地仓库 repository

本地仓库保存一次次 commit。

执行：

```bash
git commit -m "update main"
```

会把暂存区里的内容提交成本地仓库的一次 commit。

提交后，如果没有其他改动：

```bash
git status
```

会看到：

```text
nothing to commit, working tree clean
```

#### 3.4 怎么在这些区域之间切换

工作区 -> 暂存区：

```bash
git add file
```

暂存区 -> 本地仓库：

```bash
git commit -m "message"
```

从暂存区移回工作区，但保留文件修改：

```bash
git restore --staged file
```

丢弃工作区修改，恢复到上一次提交或暂存状态：

```bash
git restore file
```

注意：`git restore file` 会丢掉工作区修改，使用前要确认这些改动不需要了。

### 4. `git status --short` 两列状态

`git status --short` 的每一行前面通常有两列状态：

```text
XY 文件路径
```

其中：

- 第一列 `X`：暂存区状态。
- 第二列 `Y`：工作区状态。

可以这样记：

```text
第一列：已经 git add 进去的状态
第二列：还停留在工作区里的状态
```

#### 4.1 ` M file`

```text
 M src/main.tsx
```

第一列是空格，第二列是 `M`。

意思是：

- 暂存区没有这个修改。
- 工作区有修改。

通常表示：文件改了，但还没执行 `git add`。

#### 4.2 `M  file`

```text
M  src/main.tsx
```

第一列是 `M`，第二列是空格。

意思是：

- 修改已经进入暂存区。
- 工作区没有新的额外修改。

通常表示：文件改了，并且已经执行过 `git add`。

#### 4.3 `MM file`

```text
MM src/main.tsx
```

第一列是 `M`，第二列也是 `M`。

意思是：

- 有一部分修改已经暂存。
- 暂存之后，工作区又继续改了这个文件。

这种情况下，下一次 commit 只会提交已经暂存的那部分。后面继续改的内容不会提交，除非再次执行 `git add`。

#### 4.4 `A  file`

```text
A  src/new-file.ts
```

第一列是 `A`，第二列是空格。

意思是：新文件已经被 `git add` 加入暂存区，下一次 commit 会提交。

#### 4.5 `?? file`

```text
?? src/new-file.ts
```

意思是：未跟踪文件。

Git 看到这个文件存在，但还没有把它纳入版本管理。

#### 4.6 ` D file`

```text
 D src/old-file.ts
```

第一列是空格，第二列是 `D`。

意思是：文件在工作区被删除了，但删除操作还没有暂存。

#### 4.7 `D  file`

```text
D  src/old-file.ts
```

第一列是 `D`，第二列是空格。

意思是：删除操作已经暂存，下一次 commit 会记录这个文件删除。

#### 4.8 冲突状态

合并冲突时可能看到：

```text
UU file
AA file
DU file
```

这类状态表示 merge/rebase 过程中同一个文件存在冲突，需要先手动解决冲突，再执行：

```bash
git add file
```

把解决后的结果标记为已解决。

### 5. Git 跟踪

#### 5.1 什么是 Git 跟踪

Git 跟踪的意思是：这个文件已经被 Git 纳入版本管理。

Git 会记录它的新增、修改、删除，并在 `git status`、`git diff`、`git commit` 等命令中体现。

#### 5.2 未跟踪文件是什么

新建一个文件后，如果 Git 从未管理过它，执行：

```bash
git status --short
```

可能看到：

```text
?? notes.md
```

这表示 `notes.md` 是未跟踪文件。

#### 5.3 怎么触发跟踪

对新文件执行：

```bash
git add notes.md
```

这个文件就会进入暂存区，并开始被 Git 跟踪。

此时状态可能变成：

```text
A  notes.md
```

再执行：

```bash
git commit -m "add notes"
```

这个文件就正式进入 Git 历史。

#### 5.4 跟踪之后会怎么样

文件被跟踪后：

- 后续修改会显示为 `M`。
- 删除会显示为 `D`。
- 可以通过 `git diff` 查看改了什么。
- 可以通过 `git add` 放入暂存区。
- 可以通过 `git commit` 进入版本历史。
- 可以通过 Git 恢复到之前版本。

#### 5.5 `.gitignore` 和未跟踪文件

如果某些文件不想被 Git 跟踪，可以写进 `.gitignore`。

例如：

```text
node_modules/
.env
dist/
```

这些文件通常是依赖、环境变量、构建产物，不适合提交到仓库。

注意：`.gitignore` 主要影响未跟踪文件。

如果一个文件已经被 Git 跟踪，再把它写进 `.gitignore`，Git 仍然会继续跟踪它。需要额外处理才会停止跟踪。

#### 5.6 已经跟踪的文件，后面不想继续跟踪怎么办

如果文件已经被 Git 跟踪过，后来想让 Git 停止跟踪，但本地文件还保留，可以用：

```bash
git rm --cached file
```

然后把这个文件写进 `.gitignore`：

```text
file
```

最后提交这次变更：

```bash
git commit -m "stop tracking file"
```

这表示：

- Git 仓库以后不再管理这个文件。
- 本地工作区里的文件仍然保留。
- 其他人拉到这次提交后，这个文件会从 Git 仓库里消失。

如果是目录：

```bash
git rm -r --cached directory/
```

注意：

```bash
git rm file
```

会从 Git 跟踪和本地工作区同时删除文件。

```bash
git rm --cached file
```

只从 Git 跟踪中移除，本地文件还在。

### 6. git diff

`git diff` 主要用于查看“具体改了什么”。

`git status` 只能告诉你哪些文件变了；`git diff` 会告诉你文件里面哪些行变了。

#### 6.1 git diff

作用：

```bash
git diff
```

查看工作区中还没有暂存的改动。

也就是看：

```text
工作区 vs 暂存区
```

如果你修改了文件，但还没有执行 `git add`，可以用 `git diff` 查看具体改动。

示例：

```diff
- old line
+ new line
```

其中：

- `-` 表示删除或被替换掉的旧内容。
- `+` 表示新增或替换后的新内容。

注意：如果你已经执行了 `git add`，普通的 `git diff` 可能看不到这部分改动，因为它已经进入暂存区了。

#### 6.2 git diff --cached

作用：

```bash
git diff --cached
```

查看已经暂存、准备提交的改动。

也就是看：

```text
暂存区 vs 最新一次 commit
```

它也可以写成：

```bash
git diff --staged
```

`--cached` 和 `--staged` 在这个场景下作用基本相同。`--staged` 更容易理解：查看已经 staged 的内容。

#### 6.3 git diff --stat

作用：

```bash
git diff --stat
```

以统计摘要的形式查看工作区中还没有暂存的改动。

它不会展示每一行具体改动，而是展示每个文件改了多少行。

示例：

```text
src/main.tsx | 12 ++++++++----
1 file changed, 8 insertions(+), 4 deletions(-)
```

适合快速判断改动规模。

#### 6.4 git diff --cached --stat

作用：

```bash
git diff --cached --stat
```

查看已经暂存、准备提交的改动统计。

也就是：

```text
暂存区 vs 最新一次 commit 的统计摘要
```

它回答的是：

```text
如果我现在执行 git commit，这次提交大概会改哪些文件、多少行？
```

日常提交前很常用。

#### 6.5 多个 -- 参数是什么意思

`git diff --cached --stat` 里有两个以 `--` 开头的参数：

```bash
--cached
--stat
```

这不是一个特殊的“双 -- 命令”，而是两个长选项同时使用。

含义可以拆开看：

- `--cached`：看暂存区里的改动。
- `--stat`：用统计摘要形式展示。

合起来就是：

```text
用统计摘要形式查看暂存区里的改动
```

很多命令都可以同时带多个选项，例如：

```bash
git diff --cached --stat
git branch --all --verbose
```

只要这些选项不冲突，Git 会把它们组合起来执行。

### 7. git add

`git add` 的作用是把工作区里的改动放进暂存区。

可以理解为：

```text
我决定把这些改动放进下一次 commit 的候选清单。
```

它不是提交。真正生成提交的是 `git commit`。

常见链路：

```bash
git status
git diff
git add file
git status
git diff --cached
git commit -m "message"
```

#### 7.1 git add file

作用：

```bash
git add file
```

把指定文件当前的改动放进暂存区。

例如：

```bash
git add src/main.tsx
```

如果文件原来是：

```text
 M src/main.tsx
```

执行后会变成：

```text
M  src/main.tsx
```

含义：

- 执行前：文件在工作区改了，但没暂存。
- 执行后：文件改动已经进入暂存区，下一次 commit 会提交。

如果是新文件：

```text
?? notes.md
```

执行：

```bash
git add notes.md
```

会变成：

```text
A  notes.md
```

这表示新文件已经被 Git 跟踪，并放入暂存区。

注意：`git add file` 不只是“让 Git 跟踪新文件”，它也可以暂存已经跟踪文件的修改。

如果是新文件，`git add file` 的结果通常是 `A`。

如果是已经被 Git 跟踪过、并且提交进历史的旧文件，`git add file` 的结果通常是 `M`。

#### 7.2 git add .

作用：

```bash
git add .
```

把当前目录及其子目录下的改动放进暂存区。

通常包括：

- 新增文件。
- 修改文件。
- 删除文件。

注意：`.` 表示“当前目录”。

如果你在仓库根目录执行：

```bash
git add .
```

通常会暂存整个仓库里的改动。

如果你在 `src` 目录里执行：

```bash
git add .
```

通常只会暂存 `src` 目录及其子目录里的改动。

日常使用时，执行前建议先看：

```bash
git status
git diff
```

避免把不想提交的文件一起 add 进去。

#### 7.3 git add -A

作用：

```bash
git add -A
```

把整个仓库的新增、修改、删除全部放进暂存区。

可以理解为：

```text
暂存所有改动。
```

它和 `git add .` 很像，但重点不同：

- `git add .` 更偏当前目录范围。
- `git add -A` 明确表示整个仓库范围。

如果你想“一次性暂存所有改动”，在仓库根目录通常可以用：

```bash
git add -A
```

#### 7.4 git add -u

作用：

```bash
git add -u
```

暂存已经被 Git 跟踪过的文件的修改和删除。

它不会暂存新文件。

也就是说：

- 会处理 `M` 修改。
- 会处理 `D` 删除。
- 不会处理 `??` 未跟踪新文件。

适合场景：

```text
我只想提交已有文件的改动，不想把新建的临时文件加进去。
```

#### 7.5 git add -p

作用：

```bash
git add -p
```

交互式地选择部分改动进入暂存区。

`-p` 是 `--patch` 的简写。

它会把文件改动拆成一块一块，让你选择：

```text
y - 暂存这一块
n - 不暂存这一块
s - 尝试继续拆小
q - 退出
? - 查看帮助
```

适合场景：

```text
一个文件里有多处修改，但我只想提交其中一部分。
```

这是写干净 commit 很有用的命令。

#### 7.6 git add --dry-run

作用：

```bash
git add --dry-run .
```

预演 `git add`，只告诉你会 add 哪些文件，但不会真的放进暂存区。

也可以写成：

```bash
git add -n .
```

适合场景：

```text
我想先看看 git add . 会影响哪些文件，但还不想真的执行。
```

#### 7.7 git add --intent-to-add

作用：

```bash
git add --intent-to-add file
```

或者简写：

```bash
git add -N file
```

把一个新文件标记为“我打算之后加入 Git”，但不真正暂存文件内容。

常见用途：

```text
让新文件可以出现在 git diff 里。
```

普通未跟踪文件 `?? file` 默认不会出现在 `git diff` 中。

执行：

```bash
git add -N file
```

之后，Git 会知道这个文件存在，并允许你用：

```bash
git diff
```

查看这个新文件的内容差异。

#### 7.8 新文件 add 后为什么是 A，不是 M

假设你新建了一个文件：

```text
1.tsx
```

此时执行：

```bash
git status -s
```

会看到：

```text
?? 1.tsx
```

意思是：这是未跟踪的新文件。

然后执行：

```bash
git add 1.tsx
```

状态会变成：

```text
A  1.tsx
```

这里是 `A`，不是 `M`。

原因：

- `A` 表示新增文件会进入下一次 commit。
- `M` 表示已经被 Git 管理过并提交过的文件发生了修改。

如果你这时继续修改 `1.tsx`，但还没有再次 `git add`，状态可能变成：

```text
AM 1.tsx
```

意思是：

- 第一列 `A`：暂存区里已经有一个“新增 1.tsx”的版本。
- 第二列 `M`：工作区里又继续修改了 `1.tsx`。

如果你再次执行：

```bash
git add 1.tsx
```

状态通常会回到：

```text
A  1.tsx
```

意思是：新文件的最新内容已经全部暂存。

只有当这个文件已经被 commit 进 Git 历史之后，后续再修改它，才会显示为：

```text
 M 1.tsx
```

再执行：

```bash
git add 1.tsx
```

才会变成：

```text
M  1.tsx
```

总结：

- 新文件第一次进入暂存区：`A  file`
- 新文件暂存后又继续修改：`AM file`
- 新文件再次 add 后：`A  file`
- 文件提交过之后再修改：` M file`
- 已提交过的文件修改后再 add：`M  file`

### 8. git commit

`git commit` 的作用是把暂存区里的内容保存成一次提交。

可以理解为：

```text
把暂存区当前的快照，记录进 Git 历史。
```

日常链路是：

```bash
git status
git diff
git add file
git status
git diff --cached
git commit -m "message"
```

注意：

- `git add` 是把改动放进暂存区。
- `git commit` 才是真正生成一次本地提交。
- 没有进入暂存区的改动，默认不会被 commit。

#### 8.1 git commit

作用：

```bash
git commit
```

提交暂存区里的内容。

如果不带 `-m`，Git 通常会打开默认编辑器，让你填写提交信息。

提交信息保存并退出后，Git 会生成一次 commit。

如果暂存区为空，通常会提示没有东西可提交。

#### 8.2 git commit -m

作用：

```bash
git commit -m "message"
```

直接用命令行里的 `"message"` 作为提交信息。

示例：

```bash
git commit -m "fix login redirect"
```

这会把暂存区里的内容提交到本地仓库，并生成一条提交信息：

```text
fix login redirect
```

建议提交信息写清楚“这次改了什么”，例如：

```bash
git commit -m "fix project restore loading state"
git commit -m "add git status notes"
git commit -m "refactor auth session binding"
```

不太建议写得太空：

```bash
git commit -m "update"
git commit -m "fix"
git commit -m "change"
```

因为以后看历史时很难知道这次提交具体做了什么。

#### 8.3 git commit --amend

作用：

```bash
git commit --amend
```

修改最近一次 commit。

它常用于两种情况：

```text
1. 刚提交完，发现提交信息写错了。
2. 刚提交完，发现漏 add 了一个小文件或小改动。
```

场景一：只修改最近一次提交信息。

```bash
git commit --amend
```

Git 会打开编辑器，让你修改最近一次 commit message。

场景二：把漏掉的改动补进最近一次 commit。

```bash
git add missing-file
git commit --amend
```

这会把当前暂存区里的改动合并进最近一次 commit。

注意：`--amend` 不是在原 commit 上“原地改一点”，而是创建一个新的 commit 替换最近一次 commit。

所以 amend 后，最近一次 commit 的 commit id 会变。

#### 8.4 git commit --amend --no-edit

作用：

```bash
git commit --amend --no-edit
```

把暂存区里的改动补进最近一次 commit，但不修改提交信息。

常见场景：

```bash
git add missing-file
git commit --amend --no-edit
```

意思是：

```text
我刚才提交漏了这个文件，现在补进去，但 commit message 不变。
```

#### 8.5 amend 什么时候要小心

`git commit --amend` 会改写最近一次提交历史。

如果最近一次 commit 还只在你本地，没有 push 到远程，通常比较安全。

如果最近一次 commit 已经 push 到远程，并且别人可能已经基于它继续开发，就要小心。

因为 amend 后 commit id 会变，远程历史和本地历史会不一致。

简单记：

```text
没有 push 的最近一次 commit：可以 amend。
已经 push 且别人可能拉过：谨慎 amend。
```

如果已经 push 后还必须 amend，通常后续会涉及：

```bash
git push --force-with-lease
```

这是改写远程历史的操作，要谨慎使用。

### 9. git log

`git log` 的作用是查看提交历史。

可以理解为：

```text
查看这个仓库过去发生过哪些 commit。
```

它主要回答：

```text
谁在什么时候提交了什么？
当前分支历史上有哪些 commit？
最近一次 commit 是什么？
```

#### 9.1 git log

作用：

```bash
git log
```

查看当前分支的完整提交历史。

常见输出：

```text
commit a1b2c3d4e5f6...
Author: user <user@example.com>
Date:   Thu Jul 9 10:00:00 2026 +0800

    fix login redirect
```

这里包含：

- `commit`：提交 ID，也叫 commit hash。
- `Author`：提交作者。
- `Date`：提交时间。
- 最下面的文本：提交信息。

退出 `git log` 页面：

```text
按 q
```

#### 9.2 git log --oneline

作用：

```bash
git log --oneline
```

用一行展示一个 commit。

示例：

```text
a1b2c3d fix login redirect
b2c3d4e add git commit notes
c3d4e5f init project
```

每一行通常包含：

```text
短 commit id + 提交信息
```

这是日常最常用的历史查看方式。

#### 9.3 git log --graph --oneline --all

作用：

```bash
git log --graph --oneline --all
```

用图形方式查看所有分支的提交历史。

拆开看：

- `--graph`：显示分支/合并图。
- `--oneline`：每个 commit 显示一行。
- `--all`：显示所有分支，而不只是当前分支。

示例：

```text
* a1b2c3d fix login redirect
* b2c3d4e add git commit notes
| * e3f4g5h feature branch work
|/
* c3d4e5f init project
```

适合查看：

- 分支从哪里分出去。
- merge 后历史长什么样。
- 当前分支和其他分支的关系。

#### 9.4 git log -n

作用：

```bash
git log -n 3
```

只查看最近几条提交。

例如：

```bash
git log -n 5
git log --oneline -n 5
```

表示查看最近 5 条提交。

### 10. git show

`git show` 的作用是查看某一次提交的详细内容。

`git log` 更像看提交列表；`git show` 更像点开某一条提交，看它具体改了什么。

#### 10.1 git show

作用：

```bash
git show
```

默认查看最近一次 commit 的详细信息。

通常会显示：

- commit id。
- 作者。
- 时间。
- 提交信息。
- 这次提交具体修改了哪些内容。

#### 10.2 git show commit-id

作用：

```bash
git show commit-id
```

查看指定 commit 的详细内容。

示例：

```bash
git show a1b2c3d
```

其中 `a1b2c3d` 可以来自：

```bash
git log --oneline
```

输出里的短 commit id。

`git show commit-id` 会展示这次提交的 diff，也就是这次提交具体增删改了哪些行。

#### 10.3 git show --stat commit-id

作用：

```bash
git show --stat commit-id
```

查看指定 commit 的统计摘要。

它不会展示每一行具体改动，而是展示这次提交改了哪些文件、多少行。

示例：

```text
src/main.tsx | 12 ++++++++----
1 file changed, 8 insertions(+), 4 deletions(-)
```

适合快速判断某个 commit 的改动规模。

### 11. git push

`git push` 的作用是把本地 commit 推送到远程仓库。

可以理解为：

```text
本地仓库 repository -> 远程仓库 remote repository
```

前面的 `git commit` 只是提交到你本地电脑。

如果想让远程仓库，比如 GitHub、GitLab，看到你的提交，就需要：

```bash
git push
```

#### 11.1 git push

作用：

```bash
git push
```

把当前分支上尚未推送到远程的 commit 推送上去。

前提通常是：

- 当前分支已经和远程分支建立了关联。
- 你有远程仓库的推送权限。
- 远程没有你本地缺少的新提交，或者 Git 能正常快进推送。

日常流程：

```bash
git status
git log --oneline -n 3
git push
```

#### 11.2 git push origin branch-name

作用：

```bash
git push origin branch-name
```

把本地的 `branch-name` 分支推送到名为 `origin` 的远程仓库。

例如：

```bash
git push origin feature/login
```

这里：

- `origin`：远程仓库名字，默认常见叫 `origin`。
- `feature/login`：要推送的分支名。

#### 11.3 git push -u origin branch-name

作用：

```bash
git push -u origin branch-name
```

第一次推送新分支时常用。

`-u` 是 `--set-upstream` 的简写，意思是建立当前本地分支和远程分支的跟踪关系。

例如：

```bash
git push -u origin feature/login
```

执行后，以后你在这个分支上通常只需要：

```bash
git push
```

Git 就知道要推到哪里。

#### 11.4 git push --force-with-lease

作用：

```bash
git push --force-with-lease
```

用于强制推送，但比 `git push --force` 更安全。

常见场景：

```text
你 amend 或 rebase 之后，本地 commit 历史被改写了，需要更新远程历史。
```

例如：

```bash
git commit --amend
git push --force-with-lease
```

注意：

- 这是改写远程历史的操作。
- 如果别人已经基于旧的远程提交继续开发，可能会影响别人。
- 团队协作时不要随便用。

为什么推荐 `--force-with-lease` 而不是 `--force`：

```text
--force-with-lease 会先检查远程分支是否被别人更新过。
如果远程已经有你本地不知道的新提交，它会拒绝覆盖。
```

简单记：

```text
普通推送：git push
新分支第一次推送：git push -u origin branch-name
改写历史后的谨慎推送：git push --force-with-lease
```

#### 11.5 push 前的日常检查

push 前建议先看：

```bash
git status
git log --oneline -n 3
```

确认：

- 工作区是否干净。
- 最近几次 commit 是否是你想推的。
- 当前分支是否正确。

如果你不确定当前分支：

```bash
git branch --show-current
```

如果你不确定远程仓库：

```bash
git remote -v
```
