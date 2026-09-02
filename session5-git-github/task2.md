# Task 2 - Git Cherry-Pick

In this task I practiced creating commits on `main`, creating a new branch, and using `git cherry-pick` to bring one selected commit back to `main`.

## Commits on main Branch

I created a few commits on the `main` branch.

Commands:

```bash
git add newfile.txt
git commit -m "Add newfile after staging"

git add main-one.txt
git commit -m "Add first main file"

git add main-two.txt
git commit -m "Add second main file"
```

Then I checked the log.

Command:

```bash
git log --oneline --decorate -4
```

Output:

```text
8777b0b (HEAD -> main) Add second main file
d5e95d1 Add first main file
9b357ce Add newfile after staging
f6ecaba Update notes using commit a
```

## Created New Branch

Command:

```bash
git switch -c feature-cherry-pick
```

Output:

```text
Switched to a new branch 'feature-cherry-pick'
```

## Commits on New Branch

I made two commits on the new branch.

Commands:

```bash
git add feature-login.txt
git commit -m "Add feature login notes"

git add feature-deploy.txt
git commit -m "Add feature deploy notes"
```

Output:

```text
[feature-cherry-pick 6a1af8e] Add feature login notes
 1 file changed, 1 insertion(+)
 create mode 100644 feature-login.txt

[feature-cherry-pick 36c25e4] Add feature deploy notes
 1 file changed, 1 insertion(+)
 create mode 100644 feature-deploy.txt
```

Then I checked the log on the feature branch.

Command:

```bash
git log --oneline --decorate -4
```

Output:

```text
36c25e4 (HEAD -> feature-cherry-pick) Add feature deploy notes
6a1af8e Add feature login notes
8777b0b (main) Add second main file
d5e95d1 Add first main file
```

I selected this commit for cherry-pick:

```text
6a1af8e Add feature login notes
```

## Cherry-Pick Into main

First I switched back to `main`.

Command:

```bash
git switch main
```

Output:

```text
Switched to branch 'main'
```

Before cherry-pick, the `main` branch files were:

```text
main-one.txt
main-two.txt
newfile.txt
notes.txt
```

Then I cherry-picked only the selected commit.

Command:

```bash
git cherry-pick 6a1af8e
```

Output:

```text
[main 1ad1e66] Add feature login notes
 Date: Wed Sep 2 15:17:51 2026 +0530
 1 file changed, 1 insertion(+)
 create mode 100644 feature-login.txt
```

## Verification

I checked the log again.

Command:

```bash
git log --oneline --decorate -5
```

Output:

```text
1ad1e66 (HEAD -> main) Add feature login notes
8777b0b Add second main file
d5e95d1 Add first main file
9b357ce Add newfile after staging
f6ecaba Update notes using commit a
```

After cherry-pick, the `main` branch files were:

```text
feature-login.txt
main-one.txt
main-two.txt
newfile.txt
notes.txt
```

## What I Understood

`git cherry-pick` is used to copy one selected commit from another branch into the current branch.

In this task I did not merge the full feature branch. I only selected the commit `6a1af8e`, so only `feature-login.txt` came into `main`.
