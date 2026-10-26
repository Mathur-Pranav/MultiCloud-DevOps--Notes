## Key Outcomes

This session marked the first day of the DevOps module, transitioning from prior Linux and AWS training into Git and GitHub. Students installed Git Bash, created GitHub accounts, and successfully pushed code from a local machine to a remote GitHub repository using core Git commands. The session covered version control theory, the distinction between Git and GitHub, and practical workflows including cloning, forking, staging, committing, and pushing code. 

---

## Decisions Made

- **Git Bash** is the primary tool for all Git operations; **Git GUI** was demonstrated but deemed unnecessary since it only works on Windows and is not used in real-world workflows. 
- **GitHub Desktop** (~700 MB) was explicitly ruled out for use — it slows down code management and adds unnecessary complexity. 
- All practical labs must be performed on **personal laptops only**, not office laptops, due to potential security and vulnerability concerns (risk of exposing company code). 
- **Command-line interface (CLI)** is the standard for all Git operations going forward; GUI-based approaches will not be taught. 
- **GitLab** was noted as more popular and better than GitHub Actions for CI/CD; GitHub Actions and GitLab will be covered in later sessions. 

---

## Core Concepts Covered

### Version Control

- **Version control** maintains different snapshots of files/code over time, enabling tracking, rollback, and collaboration. 
- Demonstrated with a Google Sheets example: each edit is timestamped, and any prior version can be restored with one click. 
- Key benefits explained:
    - **Tracking**: Know who changed what and when. 
    - **Collaboration**: Multiple developers can work in parallel from different locations. 
    - **Centralized management**: Code pushed to a central repo is safe even if a developer's laptop fails or they go on leave. 
    - **Comparison**: Diff between versions (who added/deleted what) is visible per commit. 
    - **Revert**: A single `git revert` command restores a previous version (e.g., client rejects v2 and wants v1 back). 

### Git vs. GitHub

|    Aspect    |                Git                 |            GitHub             |
|--------------|------------------------------------|-------------------------------|
| Type         | Open-source software               | Cloud-hosted web service      |
| Installation | Local machine (developer's laptop) | No install; account-based     |
| Interface    | CLI (Git Bash)                     | GUI (browser)                 |
| Purpose      | Local version tracking             | Centralized remote repository |
| Ownership    | Open source                        | Purchased by **Microsoft**    |

- Git is used by an estimated **97% of developers/companies** worldwide. 
- GitHub alternatives include **GitLab** and **Bitbucket** — same Git concepts apply, only the UI/tool changes. 
- Git and GitHub communicate via **authentication/authorization mechanisms** such as SSH keys. 

### What is a Commit?

- A **commit** is the act of finalizing and saving code changes as a tracked snapshot. 
- Each commit receives a unique **hexadecimal commit ID** (tracking number) that records what changed, who changed it, and when. 
- Demonstrated on the VLC open-source GitHub repo: green `+` lines = additions; red `-` lines = deletions, all tracked per commit ID. 

### Staging Area

- The **staging area** is a waiting zone between the working directory and the remote GitHub repo — a buffer before pushing. 
- Analogy used: like an airplane on the runway preparing for takeoff — positioned and ready before launch. 
- Files must be explicitly **added** to the staging area with `git add` before they can be committed; Git won't track files it hasn't been told about. 
- The staging area allows developers to **reset wrong files or code** before the final push. 
- Cannot be bypassed — it is a mandatory part of Git's architecture. 

### Clone vs. Fork

- **Clone**: Downloads a full, fresh copy of a repository from GitHub to a local machine. Does **not** require a GitHub account. 
- **Fork**: Copies a repository **cloud-to-cloud** (GitHub account to GitHub account); requires a GitHub account. The forked copy appears under the user's own profile. 
- If the **parent (upstream) repo changes**, the forked child repo receives a notification/option to merge those changes — it does not sync automatically without user action. 
- Demonstrated live: a student (Mithun) forked the instructor's "Multicloud + DevOps with AI Challenge" repo, which already had **295 forks**. 

### README and Markdown Files

- The **[README.md](https://readme.md)** is the first file in a repository; it describes the project — how to run it, what it does, licensing, platform info, contribution guidelines. 
- **Markdown (.md)** files render formatted text (colors, headings, lists) in the GitHub UI; the raw file contains plain markdown syntax. 
- VLC's GitHub repo was used as a live example: README contains open-source license info, platform info, and contribution guidelines. 

---

## Tools Installed & Setup

### Git Installation

- **Windows**: Download ~63 MB installer from [git-scm.com](https://git-scm.com) (64-bit for Intel; ARM 64-bit for ARM processors). 
    - During setup: enable "Add Git Bash to Windows profile"; optionally add desktop icon; **disable daily auto-updates** to avoid constant update prompts. 
- **Mac**: Run `brew install git` in terminal. 
- **Linux**: Run `apt-get install git`. 
- After installation, two tools appear: **Git Bash** (primary — use this) and Git GUI (not recommended). 

### GitHub Account Creation

- Navigate to **[github.com](https://github.com)** → Sign Up → Continue with Google → choose a Google account. 
- Choose a **username** that reflects your real name (e.g., `mithun-ariya`); avoid unprofessional names as companies may request your GitHub profile. 
- Usernames **cannot be renamed** after creation. 
- Students were encouraged to update their profile with a photo and bio. 

### Visual Studio Code

- **VS Code** (~237 MB) is the recommended code editor for opening cloned repositories locally. 
- After installation, right-clicking a folder shows "Open with Code." 
- **Browser shortcut**: Replace `.com` with `.dev` in a GitHub repo URL to open it directly in a browser-based VS Code editor — useful for quick edits without local setup, but not recommended for daily use. 

---

## Git Commands Practiced

Seven core commands were covered and executed live: 

|              Command              |                                                Purpose                                                 |
|-----------------------------------|--------------------------------------------------------------------------------------------------------|
| `echo "# repo-name" >> README.md` | Creates a README.md https://readme.md file with initial content using Linux redirect                   |
| `git init`                        | Initializes Git in the local directory; creates a hidden `.git` folder for tracking                    |
| `git status`                      | Shows tracked/untracked files; untracked files appear in **red**, staged files in **green**            |
| `git add *` (or filename)         | Adds all files (wildcard `*`) or a specific file to the staging area                                   |
| `git commit -m "message"`         | Commits staged changes with a descriptive message; generates a commit ID                               |
| `git branch -M main`              | Renames/switches the current branch to `main` (GitHub uses `main`; local Git may default to `master`)  |
| `git remote add origin <URL>`     | Sets the remote GitHub repository address (the "destination address" for pushes)                       |
| `git push -u origin main`         | Pushes committed code from local machine to the remote GitHub repo                                     |

- **`git clone <URL>`**: Downloads an entire repository to the local machine. 
- **`git config --global user.name`** **/** **`user.email`**: Sets global identity so GitHub knows who is pushing code — required on first use. 
- **`git config --global --list`**: Verifies that global username and email are configured. 

---

## Common Errors & Resolutions

- **"Author identity unknown" on commit**: Global username/email not configured. Fix: run `git config --global user.name "YourName"` and `git config --global user.email "your@email.com"`, then retry. 
- **403 error on push**: Local Git not authenticated with GitHub. Fix: run the `git remote add origin` command, then push again; a browser pop-up will appear for sign-in verification. 
- **"Remote origin already exists"**: The remote was set previously (possibly incorrectly). Fix: delete the folder, start fresh, and re-run all commands from `git init`. 
- **"Source reference does not match / failed to push"**: Branch name mismatch (local is `master`, remote expects `main`). Fix: run `git branch -M main` before pushing. 
- **Wrong credentials cached (Windows)**: Go to Control Panel → User Accounts → Credential Manager → Windows Credentials → delete the old GitHub entry, then re-authenticate. 
- **Office laptop login conflicts**: If multiple GitHub accounts are cached, switch to the correct account or use a personal laptop exclusively for these labs. 

---

## Live Demonstrations

- **Version history in Google Sheets**: Showed how different dates of edits are tracked and restorable — used as an analogy for Git version control. 
- **VLC GitHub repo**: Explored a real open-source project — 761 contributors, code written 58.9% in C, commit history with per-line diff tracking, and contributor activity. 
- **Full push workflow**: Instructor created a new folder (`26 October`), opened Git Bash, created a README and a Java "Hello World" file, ran all 7 commands, and successfully pushed to GitHub. 
- **Fork demo**: Student Mithun live-forked the challenge repo; the copied repo appeared instantly in his GitHub account. 
- **Student success**: Multiple students shared their GitHub repo URLs confirming successful first code push (e.g., "Batch 45, 26 October, 1st code"). 
- **`.dev`** **browser editor**: Demonstrated opening a GitHub repo in a browser-based VS Code by changing `.com` to `.dev` in the URL. 
- **GitHub portfolio example**: Student Mohd Zubair had already built a GitHub portfolio; instructor highlighted this as a good practice and asked him to present the 5 commands used in the next class. 

---

## Pending Confirmation

- Students who encountered errors (403, branch mismatch, credential conflicts) need to **retry the full push workflow** after applying the relevant fix before the next class. 
- Students without a GitHub account or with incomplete setup need to complete account creation and first push independently. 
- **Transit Gateway** (AWS topic raised by a student) — acknowledged but not covered; will be included in the AWS revision session. 
- **Load Balancer differences** (Classic vs. ALB vs. NLB) — noted as an interview-relevant topic; deferred to a later session. 

---

## Action Items

- **All students**: Complete the first code push to GitHub (if not done in class) — retry with error fixes applied. 
- **All students**: Update GitHub profile with a real photo, name, and bio. 
- **Mohd Zubair**: Prepare a 5-minute walkthrough of the 5 Git commands used to build his portfolio — to present next class. 
- **All students**: Post a one-line session recap in the WhatsApp group summarizing what was learned (e.g., commands run, what was pushed). 
- **All students**: Do NOT use office laptops for Git/GitHub practicals — use personal laptops only. 
- **Instructor**: Cover branching in depth in the next session (branch creation, switching, merging, upstream sync for forks). 
- **Instructor**: Cover GitLab and GitHub Actions in upcoming sessions. 
- **All students**: Review all interview Q&A already uploaded to the **LMS system** for AWS and Linux revision ahead of the mock interview.



# Git & GitHub Interview Questions and Scenario-Based Answers
## Based on Day 1 of DevOps — Batch 45 | 26 October 2026

This document contains **20 core interview questions and answers** and **20 scenario-based interview questions and answers**, based on the Git and GitHub topics covered in the session notes.

---

# Part 1: 20 Git & GitHub Interview Questions and Answers

### 1. What is version control, and why is it important?

**Answer:** Version control records changes to files over time. It helps developers see what changed, who changed it, and when. It also supports collaboration, comparing versions, and restoring an earlier version when needed.

### 2. What is Git?

**Answer:** Git is open-source version control software installed on a developer's machine. It tracks local changes to files and code and lets developers create commits that represent snapshots of their work.

### 3. What is GitHub?

**Answer:** GitHub is a cloud-hosted service for storing Git repositories remotely. It helps developers share code and collaborate. Git is the version-control tool; GitHub is one service that hosts Git repositories.

### 4. What is the difference between Git and GitHub?

**Answer:**
- **Git:** Version-control software that runs locally and tracks changes.
- **GitHub:** A cloud service that hosts remote Git repositories and supports collaboration.

Git can also work with other hosting platforms, such as GitLab and Bitbucket.

### 5. What is a Git repository?

**Answer:** A Git repository is a project location where Git tracks files and their history. A local repository contains project files and Git history on your machine. A remote repository, such as one hosted on GitHub, allows the project to be shared remotely.

### 6. What is a commit in Git?

**Answer:** A commit is a saved snapshot of staged changes. Each commit has a unique identifier and records information such as the author, time, and changes included. Clear commit messages make project history easier to understand.

### 7. What is the staging area?

**Answer:** The staging area is the intermediate area where you select changes to include in the next commit. Use `git add` to stage files, then `git commit` to save the staged changes in the local repository.

### 8. What is the purpose of `git status`?

**Answer:** `git status` shows the current state of the working directory and staging area. It helps identify untracked files, modified files, and changes that are staged for commit.

```bash
git status
```

### 9. What does `git init` do?

**Answer:** `git init` initializes a Git repository in the current directory by creating the hidden `.git` directory, which stores Git's repository metadata and history.

```bash
git init
```

### 10. What is the difference between `git add` and `git commit`?

**Answer:** `git add` stages selected changes for the next commit. `git commit` saves the staged changes as a snapshot in the local repository.

```bash
git add README.md
git commit -m "Add project README"
```

### 11. What is the purpose of `git push`?

**Answer:** `git push` sends local commits to a remote repository. For example, `git push -u origin main` pushes the local `main` branch to the remote named `origin` and sets its upstream tracking relationship.

```bash
git push -u origin main
```

### 12. What is a Git remote, and what does `origin` mean?

**Answer:** A remote is a named reference to another repository, usually hosted on a platform such as GitHub. `origin` is the conventional default name for the remote from which a repository was cloned or the remote added for a project.

```bash
git remote add origin <repository-url>
```

### 13. What is the difference between `git clone` and `fork`?

**Answer:** `git clone` downloads a repository to your local machine. A fork creates a separate copy of a repository under your own hosting-platform account. A clone is a local copy; a fork is a remote copy under a different account or namespace.

### 14. Does a fork automatically receive changes from its parent repository?

**Answer:** No. A fork does not automatically synchronize every change from its parent repository. You generally need to fetch or otherwise obtain upstream changes and then merge or rebase them into your fork.

### 15. What is a branch in Git?

**Answer:** A branch is a separate line of development. It allows work to be developed independently from another branch. The session notes mention `git branch -M main`, which renames the current branch to `main`; creating, switching, and merging branches are planned for a later session.

### 16. What does `git branch -M main` do?

**Answer:** It force-renames the current branch to `main`, replacing a branch name such as `master` when needed.

```bash
git branch -M main
```

Use this when the local branch name needs to match the branch name expected by the remote repository.

### 17. What is `README.md`, and why is it useful?

**Answer:** `README.md` is a Markdown file commonly used as the main introduction to a repository. It can explain what a project does, how to run it, platform requirements, licensing, and contribution guidelines.

### 18. How do you configure your Git author name and email?

**Answer:** Configure the name and email Git should use for commits with these commands:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Verify the settings with:

```bash
git config --global --list
```

These settings identify the commit author; authentication to GitHub is a separate matter.

### 19. What is the difference between a working directory, staging area, and commit?

**Answer:**
- **Working directory:** Where you edit files.
- **Staging area:** Where you select changes for the next commit using `git add`.
- **Commit:** The saved snapshot created by `git commit`.

The typical flow is **edit → stage → commit → push**. Pushing sends local commits to the remote repository.

### 20. What is the purpose of `git revert`?

**Answer:** `git revert` creates a new commit that reverses the changes introduced by an earlier commit. It is useful when a change needs to be undone while preserving the existing history. It is different from deleting or rewriting the old commit.

---

# Part 2: 20 Scenario-Based Git & GitHub Interview Questions and Answers

### 1. You created a README file, but it does not appear in your commit. What would you check?

**Answer:** First, check the repository state:

```bash
git status
```

If the file is untracked or modified but not staged, add it and commit:

```bash
git add README.md
git commit -m "Add README"
```

Git commits only the changes that have been staged.

### 2. You run `git commit`, but Git reports “Author identity unknown.” How do you fix it?

**Answer:** Configure the Git author name and email, then retry the commit.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global --list
git commit -m "Add project files"
```

### 3. Your local branch is named `master`, but the GitHub repository expects `main`. The push fails. What do you do?

**Answer:** Rename the current local branch to `main`, then push it.

```bash
git branch -M main
git push -u origin main
```

Before pushing, verify that `origin` points to the intended repository.

### 4. You see “remote origin already exists” when adding a remote. How would you troubleshoot it?

**Answer:** Inspect the configured remotes first:

```bash
git remote -v
```

If `origin` points to the wrong URL, update it:

```bash
git remote set-url origin <correct-repository-url>
```

If it is the correct URL, do not add it again. The session notes suggested starting fresh as one recovery approach, but checking and correcting the existing remote is usually less disruptive.

### 5. You receive a 403 error when pushing to GitHub. What should you investigate?

**Answer:** Check whether you are signed in to the correct GitHub account and whether that account has write access to the repository. Also verify the remote URL and the authentication method. Re-authenticate if the wrong account or cached credentials are being used. A remote URL alone does not grant permission to push.

```bash
git remote -v
git push -u origin main
```

### 6. On Windows, Git keeps using an old GitHub account. How can you resolve it?

**Answer:** Check Windows Credential Manager for old GitHub credentials and remove the incorrect cached entry if appropriate. Then retry the Git operation and authenticate with the intended account. Confirm that the account has access to the repository.

### 7. You have made changes to three files, but you want to commit only one of them. What do you do?

**Answer:** Stage only the file you want in the commit:

```bash
git status
git add path/to/required-file
git commit -m "Update required file"
```

Avoid `git add *` when you need precise control over which files enter the commit.

### 8. You accidentally staged the wrong file. You have not committed it yet. What can you do?

**Answer:** Unstage the file while keeping its edits in your working directory:

```bash
git restore --staged path/to/file
git status
```

Then stage only the intended files. This removes the file from the staging area without discarding its local changes.

### 9. A developer's laptop fails, but the latest code was committed and pushed to GitHub. What does this demonstrate?

**Answer:** The remote repository provides a shared copy of the pushed commits. The developer can clone the repository on another machine and continue from the latest pushed state. Changes that existed only locally and were never pushed may not be available remotely.

```bash
git clone <repository-url>
```

### 10. You want to inspect an open-source project on your laptop. Do you need to fork it first?

**Answer:** Not necessarily. If you only need a local copy to inspect or run the project, clone it:

```bash
git clone <repository-url>
```

Fork it if you want a copy under your own GitHub account, for example to propose changes to a project where you do not have direct write access.

### 11. You forked an open-source repository, but the original project has new commits. How do you bring those changes into your fork?

**Answer:** Add the original repository as an upstream remote if it is not already configured, fetch its changes, and merge or rebase the appropriate upstream branch into your branch. Then push the updated branch to your fork. Review conflicts carefully before completing the integration.

```bash
git remote add upstream <original-repository-url>
git fetch upstream
```

The exact merge or rebase command depends on your branch and the upstream branch name.

### 12. You want to create a local repository and publish it to a new GitHub repository. What is the basic workflow?

**Answer:** Initialize Git, configure identity if required, create and stage files, commit them, set the branch name, add the remote, and push.

```bash
git init
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
echo "# my-project" >> README.md
git add README.md
git commit -m "Initial commit"
git branch -M main
git remote add origin <repository-url>
git push -u origin main
```

If the repository already has commits or files, inspect its state and remote configuration before running the workflow unchanged.

### 13. A teammate asks, “Who changed this line and what was removed?” How would you investigate?

**Answer:** Open the repository's commit history and inspect the relevant commit diff. GitHub displays additions and deletions in diffs, and each commit has a unique identifier. Locally, commands such as these can help:

```bash
git log --oneline
git show <commit-id>
```

### 14. A client rejects version 2 and asks you to undo its changes, but you need to preserve project history. What would you do?

**Answer:** Identify the commit that introduced the unwanted changes and use `git revert` to create a new commit that reverses it. Test the result before pushing it. This preserves the history instead of silently removing the old commit.

```bash
git log --oneline
git revert <commit-id>
git push
```

### 15. A student wants to edit a GitHub file quickly without installing an editor locally. What option was demonstrated?

**Answer:** The session demonstrated changing a repository URL from `github.com` to `github.dev` to open a browser-based VS Code editor. This can be useful for quick edits, while a local editor such as VS Code is recommended for regular development work.

### 16. Your `git push` succeeds, but a new teammate cannot see your latest changes on GitHub. What would you check?

**Answer:** Confirm that the push went to the expected remote and branch:

```bash
git remote -v
git branch --show-current
git status
git log --oneline -5
```

Then open the same repository and branch on GitHub. A commit made locally but not pushed will not appear on the remote.

### 17. You are about to run `git add *`, but your directory contains generated files and local configuration. What is the safer approach?

**Answer:** Review the file list first:

```bash
git status
```

Stage specific files or directories that belong in the commit:

```bash
git add README.md src/
```

Use a suitable `.gitignore` for files that should not be tracked. Never commit secrets, credentials, or company code to an unauthorized repository.

### 18. You need to do a Git lab, but you are using a company laptop with cached work GitHub credentials. What should you do?

**Answer:** Follow the organization's security policy. In the session, students were instructed to use personal laptops for labs to reduce the risk of exposing company code and to avoid account conflicts. Do not copy company code or credentials into a personal account.

### 19. A new repository has no project explanation, setup steps, or contribution instructions. What should you add?

**Answer:** Create or improve `README.md` with a project summary, prerequisites, installation or run instructions, usage examples, platform information, license details where applicable, and contribution guidelines. Keep the instructions accurate and easy for another developer to follow.

### 20. A new team member says GitHub and GitLab are different version-control systems, so they must learn Git again. How would you explain it?

**Answer:** Git is the version-control system. GitHub and GitLab are hosting and collaboration platforms that use Git repositories. Core Git concepts such as commits, branches, cloning, staging, and pushing are transferable, although platform interfaces and CI/CD features may differ.

---

# Quick Revision: Essential Commands

| Command | Purpose |
|---|---|
| `git init` | Initialize a local repository |
| `git status` | Inspect the working directory and staging area |
| `git add <file>` | Stage a specific file |
| `git add *` | Stage matching files in the current directory context; review carefully first |
| `git commit -m "message"` | Save staged changes as a commit |
| `git branch -M main` | Rename the current branch to `main` |
| `git remote -v` | List remote names and URLs |
| `git remote add origin <URL>` | Add a remote named `origin` |
| `git remote set-url origin <URL>` | Change the URL for `origin` |
| `git push -u origin main` | Push `main` and set upstream tracking |
| `git clone <URL>` | Download a repository to the local machine |
| `git log --oneline` | View a compact commit history |
| `git show <commit-id>` | Inspect a commit and its changes |
| `git restore --staged <file>` | Unstage a file while keeping working-directory edits |
| `git revert <commit-id>` | Create a new commit that reverses an earlier commit |
| `git config --global --list` | View global Git configuration |

## Interview Tip

When answering a scenario question, explain **what you would check first, the command or action you would use, and why**. Before changing a remote or branch, inspect the repository state. Before pushing, verify the destination and confirm you have permission to publish the code.
