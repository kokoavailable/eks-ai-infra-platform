# Git - Remove an Accidentally Committed File

Date: 2026-06-29
Status: Active

## When to Use

Use this note when a file was accidentally committed and should not remain in
the public branch history.

Examples:

- private roadmap
- local planning notes
- generated files
- machine-specific config
- files that should have been ignored

If the file contains a credential, token, password, private key, or any other
secret, do not treat history rewriting as sufficient. Rotate the secret first.

## What Happened

`ROADMAP.md` was accidentally included in the first public commit. The fix was
to keep the file locally, add it to `.gitignore`, remove it from Git tracking,
amend the commit, and force-push the corrected branch.

## Recovery Procedure

Use this when the bad file is in the latest commit and you want to keep the file
on disk.

```bash
printf "ROADMAP.md\n" > .gitignore
git rm --cached ROADMAP.md
git add .gitignore
git commit --amend --no-edit
git push --force-with-lease origin main
```

`git rm --cached` removes the file from Git tracking but does not delete the
local file.

## Verify Locally

```bash
git status --short --branch
git log --oneline --decorate --graph --all -8
git ls-files ROADMAP.md .gitignore
git check-ignore -v ROADMAP.md
git show --name-status --oneline HEAD
git status --ignored --short ROADMAP.md
```

Expected results:

```text
## main...origin/main
```

```text
.gitignore
```

```text
.gitignore:1:ROADMAP.md ROADMAP.md
```

```text
!! ROADMAP.md
```

The latest commit should not list `ROADMAP.md`.

## What Not to Do

Do not run `git pull` after amending the local commit but before force-pushing.
At that point the local branch and remote branch have divergent histories.
Pulling can reintroduce the old commit through a merge or rebase.

If Git shows this message, stop and inspect the branch state:

```text
fatal: Need to specify how to reconcile divergent branches.
```

Useful inspection commands:

```bash
git status --short --branch
git log --oneline --decorate --graph --all -12
git diff --name-status origin/main..HEAD
```

## GitHub Visibility

After `git push --force-with-lease origin main`, the normal GitHub `main`
branch history should show only the corrected commit. The removed file should
not appear in the current branch tree or normal commit list.

This does not guarantee that the old commit is impossible to access. Someone
may have already cloned or fetched it, the commit SHA may still be known, or a
hosting provider may keep internal caches.

For secrets, always rotate the secret even after rewriting history.

## Prevention

- Add private planning documents to `.gitignore` before the first commit.
- Use `git status --short` before every commit.
- Use `git show --name-status --oneline HEAD` after every first commit.
- Separate public project docs from private planning docs.
- Treat force-push as a recovery action, not a normal workflow.
