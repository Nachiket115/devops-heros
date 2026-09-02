# Task 1 - git commit -a -m

In this task I practiced `git commit -m` and `git commit -a -m`.

## First Commit

I created a file named `notes.txt`, added it to staging area, and committed it.

Command:

```bash
git add notes.txt
git commit -m "Add notes file"
```

Output:

```text
[main (root-commit) 17db4f8] Add notes file
 1 file changed, 1 insertion(+)
 create mode 100644 notes.txt
```

## Checking Difference Between Both Commands

After that I changed the already tracked file `notes.txt` and also created one new file named `newfile.txt`.

Command:

```bash
git status --short
```

Output:

```text
 M notes.txt
?? newfile.txt
```

Here `notes.txt` is modified because it is already tracked by Git.  
`newfile.txt` is untracked because it is newly created and not added yet.

## Using git commit -m

Command:

```bash
git commit -m "Try normal commit"
```

Output:

```text
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   notes.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	newfile.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`git commit -m` did not commit anything because I did not add the changes first.

## Using git commit -a -m

Command:

```bash
git commit -a -m "Update notes using commit a"
```

Output:

```text
[main f6ecaba] Update notes using commit a
 1 file changed, 1 insertion(+)
```

After this I checked status again.

Command:

```bash
git status --short
```

Output:

```text
?? newfile.txt
```

## What I Understood

`git commit -m` only commits files that are already staged using `git add`.

`git commit -a -m` automatically stages modified tracked files and commits them.

But `git commit -a -m` does not add new untracked files. New files still need `git add`.
