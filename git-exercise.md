# Git Branching and Collaboration Exercise

This hands-on exercise will guide you through essential Git workflows including branching, remote repositories, merges, conflict resolution, and advanced Git commands.

## Prerequisites

- Git installed on your system
- A GitHub account (or GitLab/Bitbucket)
- Basic command line knowledge

## Setup

Before starting, create a new directory for this exercise:

```bash
mkdir git-exercise
cd git-exercise
```

---

## Part 1: Basic Git Workflows (Steps 1-10)

### Step 1: Initialize Repository and Make First Commit

**Objective:** Create a new Git repository and make your first commit.

```bash
# Initialize a new Git repository
git init

# Configure your identity (if not already done globally)
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Create a README file
echo "# Git Exercise Project" > README.md
echo "This project demonstrates Git workflows" >> README.md

# Stage and commit
git add README.md
git commit -m "Initial commit: Add README"
```

**What you learned:** How to initialize a repository and create your first commit.

---

### Step 2: Create and Switch to a New Branch

**Objective:** Learn how to create and work with branches.

```bash
# Create a new branch called 'feature-a'
git branch feature-a

# Switch to the new branch
git checkout feature-a

# Alternative: Create and switch in one command
# git checkout -b feature-a

# Verify you're on the new branch
git branch
```

**What you learned:** Branches allow you to work on features independently without affecting the main codebase.

---

### Step 3: Make Changes on the Feature Branch

**Objective:** Make commits on your feature branch.

```bash
# Create a new file
echo "Feature A implementation" > feature-a.txt
echo "This is a new feature" >> feature-a.txt

# Stage and commit
git add feature-a.txt
git commit -m "Add feature A implementation"

# Make another change
echo "Additional feature A details" >> feature-a.txt
git add feature-a.txt
git commit -m "Expand feature A documentation"

# View your commit history
git log --oneline
```

**What you learned:** You can make multiple commits on a branch, building up your feature incrementally.

---

### Step 4: Switch Back to Main and Create Another Branch

**Objective:** Practice switching between branches and see how changes are isolated.

```bash
# Switch back to main branch
git checkout main

# Notice feature-a.txt doesn't exist here
ls

# Create and switch to another branch
git checkout -b feature-b

# Create a different file
echo "Feature B implementation" > feature-b.txt
git add feature-b.txt
git commit -m "Add feature B implementation"
```

**What you learned:** Each branch maintains its own set of changes independently.

---

### Step 5: Set Up a Remote Repository

**Objective:** Connect your local repository to a remote repository on GitHub.

```bash
# On GitHub, create a new repository (don't initialize with README)
# Then connect it to your local repo:

git remote add origin https://github.com/yourusername/git-exercise.git

# Verify the remote was added
git remote -v

# Push main branch to remote
git push -u origin main
```

**What you learned:** Remote repositories enable collaboration and backup. The `-u` flag sets up tracking between your local and remote branch.

---

### Step 6: Push Feature Branches to Remote

**Objective:** Learn to push multiple branches to the remote repository.

```bash
# Push feature-a branch
git checkout feature-a
git push -u origin feature-a

# Push feature-b branch
git checkout feature-b
git push -u origin feature-b

# View all branches (including remote)
git branch -a
```

**What you learned:** You can push any local branch to the remote repository. The `-a` flag shows both local and remote branches.

---

### Step 6b: Understanding and Creating Upstream Branches

**Objective:** Learn how upstream tracking works and how to set it up for branches.

**What is an upstream branch?**

An upstream branch (also called a tracking branch) is a remote branch that your local branch is connected to. When you set an upstream, Git knows which remote branch to push to and pull from by default.

**Benefits of upstream tracking:**
- Use `git push` and `git pull` without specifying remote and branch
- See how many commits ahead/behind you are from the remote
- Simplifies workflow for frequently updated branches

**Method 1: Set upstream during first push (most common)**

```bash
# When pushing a new branch for the first time
git push -u origin feature-name

# The -u flag (same as --set-upstream) does two things:
# 1. Pushes the branch to the remote
# 2. Sets the remote branch as the upstream

# After this, you can simply use:
git push
git pull
```

**Method 2: Set upstream for existing branch**

```bash
# If you already pushed without -u, set upstream separately
git branch --set-upstream-to=origin/feature-name

# Or the shorter version:
git branch -u origin/feature-name

# Now git push and git pull will work without arguments
```

**Method 3: Push and set upstream with different remote branch name**

```bash
# Push local branch to a differently named remote branch
git push -u origin local-branch-name:remote-branch-name

# Example: Push local 'feature' to remote 'feature-v2'
git push -u origin feature:feature-v2
```

**Check upstream configuration:**

```bash
# See which remote branch your current branch tracks
git branch -vv

# Output example:
# * feature-a    abc1234 [origin/feature-a: ahead 2] Latest commit
#   feature-b    def5678 [origin/feature-b] Another commit
#   main         ghi9012 [origin/main: behind 1] Main branch
#   no-upstream  jkl3456 No upstream configured

# The output shows:
# - Which remote branch is tracked (in brackets)
# - How many commits ahead/behind you are
```

**Practical example workflow:**

```bash
# Create a new feature branch
git checkout -b new-feature

# Make some commits
echo "New feature code" > feature.txt
git add feature.txt
git commit -m "Add new feature"

# First push - set upstream
git push -u origin new-feature

# Make more changes
echo "More changes" >> feature.txt
git add feature.txt
git commit -m "Update feature"

# Now you can just use git push (no arguments needed)
git push

# And pull updates (if others contributed)
git pull
```

**Change or remove upstream:**

```bash
# Change upstream to a different remote branch
git branch -u origin/different-branch

# Remove upstream tracking
git branch --unset-upstream

# After removing, you'll need to specify remote and branch:
git push origin feature-name
git pull origin feature-name
```

**Common scenarios:**

**Scenario 1: Branch created locally but not yet on remote**
```bash
git checkout -b local-feature
# Make commits...
git push -u origin local-feature  # Creates remote branch and sets upstream
```

**Scenario 2: Branch exists on remote, you want to work on it**
```bash
# Fetch all remote branches
git fetch origin

# Create local branch that tracks the remote one
git checkout -b feature-x origin/feature-x
# Or shorter: git checkout feature-x (Git auto-creates tracking branch)

# Upstream is automatically set
```

**Scenario 3: Pushed without -u flag, now want to set upstream**
```bash
git push origin my-branch  # Pushed but no upstream
git branch -u origin/my-branch  # Now set upstream
```

**Scenario 4: Multiple remotes (e.g., origin and upstream for forked repos)**
```bash
# Add upstream remote (original repo you forked from)
git remote add upstream https://github.com/original/repo.git

# Set your branch to track original repo's main
git branch -u upstream/main

# Or track your fork's main
git branch -u origin/main
```

**What you learned:** Upstream tracking simplifies your Git workflow by connecting local and remote branches, allowing you to use shorter commands and see sync status at a glance.

---

### Step 7: Merge a Feature Branch (Fast-Forward)

**Objective:** Merge a feature branch into main using a fast-forward merge.

```bash
# Switch to main
git checkout main

# Merge feature-a (this will be a fast-forward merge)
git merge feature-a

# View the log to see the merge
git log --oneline --graph

# Push updated main to remote
git push origin main
```

**What you learned:** A fast-forward merge happens when the main branch hasn't changed since you created the feature branch. Git simply moves the main pointer forward.

---

### Step 8: Create a Merge Commit (No Fast-Forward)

**Objective:** Merge another branch and create an explicit merge commit.

```bash
# Make a change on main first
echo "Direct change to main branch" >> README.md
git add README.md
git commit -m "Update README on main"

# Now merge feature-b (this creates a merge commit)
git merge feature-b --no-ff -m "Merge feature-b into main"

# View the branching structure
git log --oneline --graph --all

# Push to remote
git push origin main
```

**What you learned:** When both branches have new commits, Git creates a merge commit to combine the histories. The `--no-ff` flag forces a merge commit even when fast-forward is possible.

---

### Step 9: Simulate and Resolve a Merge Conflict

**Objective:** Experience and resolve a merge conflict.

```bash
# Create a new branch
git checkout -b feature-c

# Edit README with conflicting content
echo "Feature C adds this line" >> README.md
git add README.md
git commit -m "Feature C: Update README"

# Switch to main and create conflicting change
git checkout main
echo "Main branch adds this different line" >> README.md
git add README.md
git commit -m "Update README on main branch"

# Try to merge feature-c (this will create a conflict)
git merge feature-c
```

At this point, Git will report a conflict. The README.md file will contain conflict markers:

```
<<<<<<< HEAD
Main branch adds this different line
=======
Feature C adds this line
>>>>>>> feature-c
```

**Resolve the conflict:**

```bash
# Open README.md and manually resolve the conflict
# Remove the conflict markers and decide what to keep
# For example, keep both lines:

# Edit the file to look like:
# Main branch adds this different line
# Feature C adds this line

# Stage the resolved file
git add README.md

# Complete the merge
git commit -m "Merge feature-c: Resolve README conflict"

# Push to remote
git push origin main
```

**What you learned:** Conflicts occur when the same lines are modified in different branches. Git requires manual intervention to resolve them.

---

### Step 9b: Git Reset - Undoing Changes and Moving History

**Objective:** Learn how to use git reset to undo commits and move the branch pointer.

**What is git reset?**

`git reset` moves the current branch pointer to a different commit, effectively "undoing" commits. It's one of the most powerful (and potentially dangerous) Git commands. The key is understanding the three modes: `--soft`, `--mixed`, and `--hard`.

**The Three Modes of Reset:**

1. **--soft**: Moves branch pointer, keeps changes staged
2. **--mixed** (default): Moves branch pointer, keeps changes unstaged
3. **--hard**: Moves branch pointer, discards all changes

**Visual representation:**
```
Before: main -> A -> B -> C (HEAD)
After git reset --soft HEAD~1: main -> A -> B (HEAD), changes from C staged
After git reset --mixed HEAD~1: main -> A -> B (HEAD), changes from C unstaged
After git reset --hard HEAD~1: main -> A -> B (HEAD), changes from C deleted
```

**Method 1: Soft Reset (Keep Changes Staged)**

```bash
# Create some commits to practice with
git checkout main
echo "First change" >> reset-demo.txt
git add reset-demo.txt
git commit -m "Commit 1"

echo "Second change" >> reset-demo.txt
git add reset-demo.txt
git commit -m "Commit 2"

echo "Third change" >> reset-demo.txt
git add reset-demo.txt
git commit -m "Commit 3"

# View the commits
git log --oneline

# Soft reset - undo last commit but keep changes staged
git reset --soft HEAD~1

# Check status - changes are still staged
git status

# The file still has all three changes
cat reset-demo.txt

# You can now re-commit with a different message
git commit -m "Commit 2 and 3 combined"
```

**When to use --soft:**
- Combine multiple commits into one
- Change the last commit message with different content
- Reorganize recent commits before pushing

**Method 2: Mixed Reset (Keep Changes Unstaged - Default)**

```bash
# Make another commit
echo "Fourth change" >> reset-demo.txt
git add reset-demo.txt
git commit -m "Commit 4"

# Mixed reset (--mixed is the default)
git reset HEAD~1
# Or explicitly: git reset --mixed HEAD~1

# Check status - changes exist but are unstaged
git status

# File still has the changes
cat reset-demo.txt

# You need to add and commit again
git add reset-demo.txt
git commit -m "Commit 4 revised"
```

**When to use --mixed:**
- Undo commits and re-stage changes selectively
- Split a large commit into smaller ones
- Review changes before recommitting

**Method 3: Hard Reset (Discard All Changes)**

**⚠️ WARNING:** This permanently deletes uncommitted changes!

```bash
# Make a commit we want to completely remove
echo "Bad change" >> reset-demo.txt
git add reset-demo.txt
git commit -m "Bad commit"

# Hard reset - completely discard the commit and changes
git reset --hard HEAD~1

# Check status - working directory is clean
git status

# The file no longer has "Bad change"
cat reset-demo.txt
```

**When to use --hard:**
- Completely discard unwanted commits (use with caution!)
- Clean up experimental changes
- Return to a known good state
- Discard all local uncommitted changes

**Common Reset Scenarios:**

**Scenario 1: Undo last commit, keep all changes**
```bash
git reset HEAD~1
# Changes are now unstaged, commit is gone
```

**Scenario 2: Undo last 3 commits, keep changes staged**
```bash
git reset --soft HEAD~3
# All changes from 3 commits are staged, ready to re-commit
```

**Scenario 3: Completely remove last commit and all its changes**
```bash
git reset --hard HEAD~1
# Commit and changes are permanently gone
```

**Scenario 4: Reset to a specific commit**
```bash
# View commit history
git log --oneline

# Reset to a specific commit hash
git reset --hard abc1234

# Or reset to a branch
git reset --hard origin/main
```

**Scenario 5: Discard all local uncommitted changes**
```bash
# Discard all changes in working directory
git reset --hard HEAD

# This is equivalent to
# git checkout -- .  (old way)
# git restore .      (newer way)
```

**Scenario 6: Unstage files (without losing changes)**
```bash
# You accidentally staged files
git add unwanted-file.txt

# Unstage it
git reset HEAD unwanted-file.txt
# Or: git reset unwanted-file.txt

# File is no longer staged but changes remain
```

**Reset vs Revert:**

```bash
# git reset: Moves branch pointer backwards (rewrites history)
git reset HEAD~1  # Removes commit from history

# git revert: Creates a new commit that undoes changes (preserves history)
git revert HEAD   # Keeps commit, adds new "undo" commit
```

**When to use reset vs revert:**
- **Use reset**: For local commits not yet pushed to remote
- **Use revert**: For commits already pushed to shared branches

**Recovering from a reset (if needed):**

```bash
# If you accidentally reset, you can recover using reflog
git reflog

# Find the commit you want to return to
# Example output:
# abc1234 HEAD@{0}: reset: moving to HEAD~1
# def5678 HEAD@{1}: commit: The commit you just reset

# Reset back to that commit
git reset --hard def5678

# Your commit is back!
```

**Reset with file paths:**

```bash
# Reset specific files to a previous commit
git reset HEAD~1 -- file.txt

# This updates the staging area with file.txt from HEAD~1
# The working directory keeps current version

# To also update working directory:
git checkout HEAD~1 -- file.txt
```

**Practical example: Clean up messy commit history**

```bash
# You made 5 small commits that should be one
git log --oneline
# Shows: 5 commits with small changes

# Reset to before all 5 commits, keeping changes staged
git reset --soft HEAD~5

# Now create one clean commit
git commit -m "Feature X: Complete implementation"

# You now have 1 commit instead of 5
```

**Safety tips:**

1. **Never reset public history** - Don't reset commits that have been pushed to a shared branch
2. **Use reset for local work only** - For shared branches, use `git revert` instead
3. **Check twice before --hard** - Hard reset permanently deletes changes
4. **Use reflog as safety net** - You can recover from mistakes using `git reflog`
5. **Create a backup branch** - Before risky operations: `git branch backup`

**Quick Reference:**

| Command | Branch Pointer | Staging Area | Working Directory |
|---------|---------------|--------------|-------------------|
| `--soft` | Moved | Unchanged | Unchanged |
| `--mixed` | Moved | Reset | Unchanged |
| `--hard` | Moved | Reset | Reset |

**What you learned:** Git reset is a powerful tool for undoing commits and managing your branch history. Understanding the three modes (soft, mixed, hard) lets you choose the right level of undo for your situation.

---

### Step 9c: Checking Out Previous Commits and Detached HEAD

**Objective:** Learn how to view previous commits and understand detached HEAD state.

**What is checking out a commit?**

`git checkout <commit>` allows you to view your repository at any previous point in time. This puts you in a "detached HEAD" state, which means HEAD points directly to a commit instead of a branch. This is useful for inspecting old code, testing historical versions, or creating new branches from past commits.

**Understanding HEAD:**

- **Normal state**: HEAD → branch → commit
  ```
  HEAD → main → abc1234
  ```
- **Detached HEAD state**: HEAD → commit directly
  ```
  HEAD → abc1234 (not on any branch)
  ```

**Method 1: Checkout a Specific Commit**

```bash
# View commit history to find commit hashes
git log --oneline

# Example output:
# abc1234 (HEAD -> main) Latest commit
# def5678 Previous commit
# ghi9012 Earlier commit
# jkl3456 First commit

# Check out an older commit
git checkout def5678

# You'll see a message about "detached HEAD state"
# Now your working directory shows files as they were at that commit

# View files at this point in history
ls
cat some-file.txt

# You can compile/run code from this point
# This is read-only exploration - perfect for testing old versions
```

**Method 2: Navigate Using Relative References**

```bash
# Go back 1 commit from current HEAD
git checkout HEAD~1

# Go back 2 commits
git checkout HEAD~2

# Go back 3 commits
git checkout HEAD~3

# Or use the caret (^) for first parent
git checkout HEAD^
git checkout HEAD^^   # Two commits back
```

**Method 3: Checkout by Tag or Branch**

```bash
# Checkout a tagged version
git checkout v1.0.0

# Checkout a specific branch's commit
git checkout feature-branch

# Checkout a remote branch's state
git checkout origin/main
```

**What You Can Do in Detached HEAD State:**

**1. Inspect and explore:**
```bash
# Look at old files
git checkout abc1234
cat old-version.txt

# Check the git log from this point
git log --oneline

# View differences from current
git diff main
```

**2. Test old code:**
```bash
# Checkout an old version
git checkout v2.0.0

# Build and test it
npm install
npm test

# If it works, you know the bug came later
```

**3. Create a new branch from this point:**
```bash
# You're at an old commit in detached HEAD
git checkout abc1234

# Create a new branch from here
git checkout -b fix-from-old-version
# Or: git switch -c fix-from-old-version

# Now you're on a branch and can commit normally
```

**Returning to HEAD (Getting Back to Your Branch):**

**Method 1: Return to the branch you were on**
```bash
# Simply checkout the branch name
git checkout main

# Or use the newer switch command
git switch main

# You're back to the latest commit on main
```

**Method 2: Use the previous reference**
```bash
# Go back to where you were before
git checkout -

# This toggles between current and previous location
# Similar to "cd -" in the terminal
```

**Method 3: If you forgot which branch you were on**
```bash
# View reflog to see where you came from
git reflog

# Output shows recent movements:
# abc1234 HEAD@{0}: checkout: moving from main to abc1234
# def5678 HEAD@{1}: commit: Latest commit on main
# ...

# Return to main (or whatever branch you were on)
git checkout main
```

**Method 4: Using git switch (modern approach)**
```bash
# Switch back to your previous branch
git switch -

# Or explicitly switch to a branch
git switch main
```

**Common Scenarios:**

**Scenario 1: Inspecting an old bug report**
```bash
# Bug was reported in version from 2 weeks ago
git log --since="2 weeks ago" --oneline

# Checkout that commit
git checkout <commit-hash>

# Test and investigate
# ... do your testing ...

# Return to current work
git checkout main
```

**Scenario 2: Finding when a bug was introduced (with git bisect)**
```bash
# Start bisect
git bisect start

# Mark current as bad
git bisect bad

# Find a good commit (e.g., 10 commits ago)
git bisect good HEAD~10

# Git checks out a commit in the middle
# Test it and mark as good or bad
git bisect bad   # or git bisect good

# Repeat until git finds the problematic commit
# ...

# When done, return to normal state
git bisect reset
```

**Scenario 3: Creating a hotfix from a production release**
```bash
# Checkout the production version (tagged as v2.1.0)
git checkout v2.1.0

# Create a hotfix branch from this point
git checkout -b hotfix-2.1.1

# Make fixes
echo "Critical fix" >> fix.txt
git add fix.txt
git commit -m "Hotfix: Resolve critical issue"

# Now you have a branch from the old version
# You can merge this to main or release it separately
```

**Scenario 4: Comparing current code with old version**
```bash
# Checkout old commit
git checkout abc1234

# In another terminal, compare
git diff main

# Or save the old version of a file
cp important-file.txt /tmp/old-version.txt

# Return to main
git checkout main

# Compare files
diff important-file.txt /tmp/old-version.txt
```

**Scenario 5: Accidentally made commits in detached HEAD**
```bash
# You were in detached HEAD and made commits
git checkout abc1234
# ... made changes ...
git commit -m "Oops, committed in detached HEAD"

# Git warns you these commits might be lost
# Create a branch to save them
git branch save-my-work

# Or create and switch in one command
git checkout -b save-my-work

# Now your commits are safe on a branch
```

**Understanding the Warning Message:**

When you checkout a commit, Git shows:
```
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command.
```

**This means:**
- ✅ Safe to explore and view files
- ✅ Safe to test and compile
- ⚠️ Commits made here will be lost unless you create a branch
- ✅ Can always return to your branch safely

**Detached HEAD vs Reset:**

| Operation | Use Case | Changes History? |
|-----------|----------|------------------|
| `git checkout <commit>` | View old code, explore | No - just moves HEAD temporarily |
| `git reset <commit>` | Undo commits, rewrite history | Yes - moves branch pointer |

**Visual Example:**

```
Initial state:
  main → C3 ← HEAD
         ↑
        C2
         ↑
        C1

After git checkout C1:
  main → C3
         ↑
        C2
         ↑
        C1 ← HEAD (detached)

After git checkout main:
  main → C3 ← HEAD (back to normal)
         ↑
        C2
         ↑
        C1
```

**Quick Command Reference:**

```bash
# View history
git log --oneline --graph --all

# Checkout old commit
git checkout <commit-hash>
git checkout HEAD~3
git checkout v1.0.0

# Return to branch
git checkout main
git checkout -          # Go to previous location
git switch main         # Modern command

# Create branch from detached HEAD
git checkout -b branch-name
git switch -c branch-name

# View where you've been
git reflog
```

**Safety Tips:**

1. **Don't commit in detached HEAD** unless you create a branch first
2. **Use checkout for exploration only** - use reset to modify history
3. **Always return to a branch** before continuing work
4. **Save unexpected commits** by creating a branch before switching away
5. **Use reflog as a safety net** to find your way back if lost

**What you learned:** Checking out previous commits lets you time-travel through your repository's history. Understanding detached HEAD state helps you explore safely while knowing how to return to normal work. This is essential for debugging, testing old versions, and understanding your project's evolution.

---

### Step 10: Pull Changes from Remote

**Objective:** Simulate collaborative work by pulling changes from the remote.

```bash
# First, simulate another developer's work
# You can do this by editing directly on GitHub or:

# Create a new branch and push it
git checkout -b teammate-feature
echo "A teammate's contribution" > teammate.txt
git add teammate.txt
git commit -m "Add teammate contribution"
git push -u origin teammate-feature

# Merge it on GitHub using a Pull Request, or merge locally:
git checkout main
git merge teammate-feature
git push origin main

# Now simulate being on another computer
# Go to a different directory
cd ..
mkdir git-clone
cd git-clone

# Clone the repository
git clone https://github.com/yourusername/git-exercise.git
cd git-exercise

# You now have all the changes
ls

# Back in the original directory, make a change and push
cd ../../git-exercise
echo "New update from original location" >> README.md
git add README.md
git commit -m "Add update to README"
git push origin main

# In the cloned directory, pull the changes
cd ../git-clone/git-exercise
git pull origin main

# View the updated files
cat README.md
```

**What you learned:** `git pull` fetches changes from the remote repository and merges them into your current branch. It's essentially `git fetch` + `git merge`.

---

### Step 10b: Git Revert - Safely Undoing Commits

**Objective:** Learn how to undo commits that have been pushed to shared branches without rewriting history.

**What is git revert?**

`git revert` creates a new commit that undoes the changes from a previous commit. Unlike `git reset` which rewrites history, `git revert` preserves the original commit and adds a new "undo" commit. This makes it safe to use on shared branches where others may have already pulled your commits.

**Reset vs Revert - The Critical Difference:**

```
git reset:  A → B → C → D    (reset to B)
            A → B             ← Commits C and D are removed from history

git revert: A → B → C → D    (revert D)
            A → B → C → D → D' ← New commit D' undoes changes from D
```

**When to use reset vs revert:**
- **Use `git reset`**: For local commits that haven't been pushed
- **Use `git revert`**: For commits already pushed to shared branches
- **Rule of thumb**: If others might have your commits, use revert

**Method 1: Revert the Most Recent Commit**

```bash
# Make a commit we'll want to undo
git checkout main
echo "This change was a mistake" >> mistake.txt
git add mistake.txt
git commit -m "Add problematic feature"

# Push it to remote (simulating shared work)
git push origin main

# Oops! We need to undo this
# Use revert to create an undo commit
git revert HEAD

# Git opens an editor with a default message:
# "Revert "Add problematic feature""
# Save and close

# View the history
git log --oneline

# You'll see:
# xyz9876 Revert "Add problematic feature"
# abc1234 Add problematic feature
# ...

# Push the revert commit
git push origin main
```

**What happened:**
- Original commit stays in history
- New commit undoes the changes
- Other developers can pull safely
- Complete audit trail of what happened

**Method 2: Revert a Specific Commit (Not the Latest)**

```bash
# View commit history
git log --oneline

# Example output:
# e5f6g7h Latest commit
# d4e5f6g Another commit
# c3d4e5f The problematic commit we want to undo
# b2c3d4e Earlier commit
# a1b2c3d First commit

# Revert a specific commit
git revert c3d4e5f

# Git creates a commit that undoes changes from c3d4e5f
# Note: This only undoes that specific commit, not everything after it
```

**Method 3: Revert Multiple Commits**

```bash
# Revert a range of commits (oldest first)
git revert <oldest-commit>..<newest-commit>

# Example: Revert commits from B to D (not including B)
git revert b2c3d4e..d4e5f6g

# Revert multiple specific commits
git revert c3d4e5f d4e5f6g e5f6g7h

# Each commit gets its own revert commit
```

**Method 4: Revert Without Auto-Committing**

```bash
# Revert but stage changes without committing
git revert --no-commit HEAD

# Or revert multiple commits into one revert commit
git revert --no-commit HEAD~3..HEAD

# Review the changes
git status
git diff --cached

# Make any adjustments needed
# Then commit manually
git commit -m "Revert last 3 commits due to bug XYZ"
```

**Handling Merge Commits:**

Merge commits have multiple parents, so you need to specify which parent to revert to:

```bash
# Revert a merge commit
git revert -m 1 <merge-commit-hash>

# -m 1 means: keep changes from the first parent (usually main)
# -m 2 means: keep changes from the second parent (the merged branch)

# Example scenario:
# You merged feature-branch into main, but need to undo it
git log --oneline --graph
# Find the merge commit hash

git revert -m 1 abc1234
```

**Common Scenarios:**

**Scenario 1: Undo a pushed commit**
```bash
# You pushed a commit to main with a bug
git log --oneline
# abc1234 (HEAD -> main, origin/main) Buggy commit

# Revert it
git revert abc1234

# Push the revert
git push origin main

# Everyone can safely pull the fix
```

**Scenario 2: Undo multiple related commits**
```bash
# You pushed 3 commits that together introduced a problem
git log --oneline
# Shows: commit3, commit2, commit1

# Revert all three as a single revert commit
git revert --no-commit HEAD~2..HEAD
git commit -m "Revert commits 1-3: Rollback problematic feature"

# Push the revert
git push origin main
```

**Scenario 3: Revert a revert (re-apply changes)**
```bash
# You reverted a commit, but now you want it back
git log --oneline
# xyz9876 Revert "Add feature X"
# abc1234 Add feature X

# Revert the revert commit
git revert xyz9876

# This re-applies the original changes from abc1234
```

**Scenario 4: Revert an old commit without affecting newer commits**
```bash
# You have: A → B → C → D → E
# You want to undo B but keep C, D, E

# Revert just commit B
git revert <commit-B-hash>

# Git will:
# 1. Try to apply the inverse of B
# 2. May cause conflicts if C, D, or E touched the same lines
# 3. You resolve conflicts if needed
# 4. Result: A → B → C → D → E → B'
```

**Handling Revert Conflicts:**

When reverting causes conflicts:

```bash
# Start the revert
git revert abc1234

# Git reports conflicts
# CONFLICT (content): Merge conflict in file.txt

# Open the conflicted files and resolve
# Look for conflict markers:
# <<<<<<< HEAD
# Current version
# =======
# Reverted version
# >>>>>>> parent of abc1234... commit message

# After resolving conflicts
git add file.txt

# Continue the revert
git revert --continue

# Or abort if you change your mind
# git revert --abort
```

**Revert vs Reset vs Checkout - Complete Comparison:**

| Command | Changes History? | Safe for Shared Branches? | Use Case |
|---------|------------------|---------------------------|----------|
| `git revert` | No (adds new commit) | ✅ Yes | Undo pushed commits |
| `git reset` | Yes (removes commits) | ❌ No | Undo local commits |
| `git checkout` | No (just views) | ✅ Yes | View old versions |

**Visual Example:**

```
Initial state:
A → B → C → D (main, origin/main)

After git reset --hard B (❌ DON'T do this after pushing):
A → B (main)
      C → D (origin/main) ← Others still have these!

After git revert D (✅ Correct approach):
A → B → C → D → D' (main, origin/main)
                 ↑ Undoes D's changes
```

**Interactive Revert:**

For reviewing what will be reverted:

```bash
# See what would be reverted without doing it
git revert --no-commit HEAD
git diff --cached

# If it looks good, commit
git commit

# If not, abort
git reset --hard HEAD
```

**Best Practices for Revert:**

1. **Always revert public commits** - Never reset shared history
2. **Write clear revert messages** - Explain why you're reverting
3. **Test after reverting** - Ensure the revert didn't break anything
4. **Consider the impact** - Reverting old commits may cause conflicts
5. **Communicate with team** - Let others know about significant reverts
6. **Revert merge commits carefully** - Specify the correct parent with `-m`

**Common Revert Patterns:**

```bash
# Revert last commit
git revert HEAD

# Revert second-to-last commit
git revert HEAD~1

# Revert a specific commit
git revert abc1234

# Revert multiple commits as one
git revert --no-commit HEAD~3..HEAD
git commit -m "Revert last 3 commits"

# Revert and edit the commit message
git revert HEAD --edit

# Revert without opening editor (use default message)
git revert HEAD --no-edit

# Revert a merge commit (keep main branch changes)
git revert -m 1 <merge-commit>
```

**Troubleshooting Revert Issues:**

**Issue 1: "error: commit X is a merge but no -m option was given"**
```bash
# Solution: Specify which parent to keep
git revert -m 1 <commit-hash>
```

**Issue 2: Revert causes too many conflicts**
```bash
# Solution: Abort and consider alternative approaches
git revert --abort

# Alternative: Create a new commit that fixes the issue
# instead of reverting old changes
```

**Issue 3: Need to revert a revert**
```bash
# Just revert the revert commit
git revert <revert-commit-hash>
```

**Real-World Example:**

```bash
# Monday: Deploy feature to production
git checkout main
git merge feature-x
git push origin main

# Tuesday: Feature causes issues in production
# Need to quickly revert

# Find the merge commit
git log --oneline --graph
# ab12cd3 Merge branch 'feature-x'

# Revert the merge
git revert -m 1 ab12cd3 -m "Revert feature-x: causes production issues"

# Push immediately
git push origin main

# Production is now stable again
# Team can investigate and fix feature-x on the branch
# Then re-merge when ready
```

**What you learned:** Git revert is the safe way to undo commits that have been shared with others. It preserves history by creating new commits that undo changes, making it perfect for production branches and collaborative work. Unlike reset, revert never rewrites history, preventing conflicts for team members who have already pulled your commits.

---

## Part 2: Advanced Git Commands (Steps 11-13)

### Step 11: Git Rebase - Rewriting History

**Objective:** Learn how to use rebase to create a cleaner, linear history.

**What it does:** `git rebase` replays commits from one branch onto another. Unlike merge, which creates a merge commit, rebase rewrites history by creating new commits with the same changes. This creates a linear history that's easier to read.

**When to use it:**
- Before merging a feature branch to create a cleaner history
- To update your feature branch with the latest main branch changes
- To clean up local commits before pushing

**WARNING:** Never rebase commits that have been pushed to a shared remote repository, as it rewrites history and can cause problems for other developers.

```bash
# Create a feature branch
git checkout -b rebase-demo
echo "Rebase feature line 1" > rebase.txt
git add rebase.txt
git commit -m "Rebase demo: Add line 1"

echo "Rebase feature line 2" >> rebase.txt
git add rebase.txt
git commit -m "Rebase demo: Add line 2"

# Switch to main and add commits
git checkout main
echo "Main branch update 1" >> README.md
git add README.md
git commit -m "Main: Update 1"

echo "Main branch update 2" >> README.md
git add README.md
git commit -m "Main: Update 2"

# Now rebase the feature branch onto main
git checkout rebase-demo
git rebase main

# View the linear history
git log --oneline --graph

# If you want to see the difference, compare with merge:
# git checkout main
# git merge rebase-demo --no-ff
```

**Handling rebase conflicts:**

If conflicts occur during rebase:

```bash
# Resolve the conflict in the affected files
# Then stage the resolved files
git add <resolved-files>

# Continue the rebase
git rebase --continue

# Or abort if you want to start over
# git rebase --abort
```

**Interactive rebase** (for cleaning up commits):

```bash
# Interactively rebase the last 3 commits
git rebase -i HEAD~3

# This opens an editor where you can:
# - pick: keep the commit as is
# - reword: change the commit message
# - squash: combine with previous commit
# - fixup: like squash but discard commit message
# - drop: remove the commit
```

---

### Step 12: Git Stash - Temporarily Saving Work

**Objective:** Learn to save work-in-progress without committing.

**What it does:** `git stash` temporarily saves your uncommitted changes (both staged and unstaged) and reverts your working directory to match the HEAD commit. This is useful when you need to quickly switch contexts without committing incomplete work.

**When to use it:**
- You need to switch branches but have uncommitted changes
- You want to pull changes but have local modifications
- You need to test something on a clean working directory
- You want to save experimental changes without committing

```bash
# Start working on something
git checkout main
echo "Work in progress" >> wip.txt
echo "More changes" >> README.md
git add wip.txt

# Check status (you have changes)
git status

# Suddenly you need to switch branches or pull changes
# Stash your work
git stash save "WIP: Working on new feature"

# Your working directory is now clean
git status

# List all stashes
git stash list

# Do other work (switch branches, pull changes, etc.)
git checkout feature-b
# ... do some work ...
git checkout main

# Restore your stashed changes
git stash pop

# The changes are back
git status

# If you want to keep the stash after applying:
# git stash apply instead of pop
```

**Advanced stash operations:**

```bash
# Stash including untracked files
git stash save -u "Include untracked files"

# Apply a specific stash (not the most recent)
git stash list  # See list with indices
git stash apply stash@{1}  # Apply the second stash

# View stash contents without applying
git stash show -p stash@{0}

# Drop a specific stash
git stash drop stash@{0}

# Clear all stashes
git stash clear

# Create a branch from a stash
git stash branch feature-from-stash stash@{0}
```

---

### Step 13: Git Cherry-Pick - Selectively Applying Commits

**Objective:** Learn to apply specific commits from one branch to another.

**What it does:** `git cherry-pick` allows you to apply the changes from specific commits onto your current branch. Unlike merge or rebase which bring over all commits, cherry-pick lets you select individual commits.

**When to use it:**
- Apply a bug fix from one branch to another without merging everything
- Port a feature commit to a different release branch
- Apply a commit that was made on the wrong branch
- Selectively bring changes from an experimental branch

```bash
# Create a branch with several commits
git checkout -b cherry-demo
echo "Commit 1" > cherry.txt
git add cherry.txt
git commit -m "Cherry demo: Commit 1"

echo "Commit 2" >> cherry.txt
git add cherry.txt
git commit -m "Cherry demo: Commit 2"

echo "Commit 3" >> cherry.txt
git add cherry.txt
git commit -m "Cherry demo: Commit 3"

# Note the commit hashes
git log --oneline

# Switch to main branch
git checkout main

# Cherry-pick only the second commit (use the actual hash)
# Replace <commit-hash> with the hash of "Commit 2"
git cherry-pick <commit-hash>

# View the result
cat cherry.txt  # Should only have "Commit 1" and "Commit 2"
git log --oneline

# Cherry-pick multiple commits
git cherry-pick <hash1> <hash2> <hash3>

# Cherry-pick a range of commits (exclusive of <hash1>)
# git cherry-pick <hash1>..<hash2>
```

**Handling cherry-pick conflicts:**

```bash
# If conflicts occur during cherry-pick:
# 1. Resolve the conflicts in the affected files
# 2. Stage the resolved files
git add <resolved-files>

# 3. Continue the cherry-pick
git cherry-pick --continue

# Or abort if needed
# git cherry-pick --abort

# To cherry-pick without auto-committing (useful for review):
git cherry-pick -n <commit-hash>
# Make any adjustments
git commit
```

**Cherry-pick use case example:**

```bash
# Scenario: You have a critical bug fix on a feature branch
# but don't want to merge the entire feature yet

git checkout feature-branch
# Make bug fix
git add .
git commit -m "Fix critical authentication bug"
# Note the commit hash: abc123

# Apply just this fix to main
git checkout main
git cherry-pick abc123
git push origin main

# Apply the same fix to a release branch
git checkout release-1.0
git cherry-pick abc123
git push origin release-1.0
```

---

## Summary of Commands

### Basic Operations
- `git init` - Initialize a repository
- `git clone <url>` - Clone a remote repository
- `git add <file>` - Stage changes
- `git commit -m "message"` - Commit staged changes
- `git status` - Check repository status
- `git log --oneline --graph` - View commit history

### Branching
- `git branch` - List branches
- `git branch <name>` - Create a new branch
- `git checkout <branch>` - Switch to a branch
- `git checkout -b <branch>` - Create and switch to a new branch
- `git branch -d <branch>` - Delete a branch

### Remote Operations
- `git remote add origin <url>` - Add a remote repository
- `git push -u origin <branch>` - Push branch to remote and set upstream tracking
- `git push origin <branch>` - Push commits to remote
- `git pull origin <branch>` - Fetch and merge remote changes
- `git fetch` - Download remote changes without merging
- `git branch -u origin/<branch>` - Set upstream for current branch
- `git branch --set-upstream-to=origin/<branch>` - Set upstream (verbose version)
- `git branch -vv` - View branches with upstream tracking info
- `git branch --unset-upstream` - Remove upstream tracking

### Merging
- `git merge <branch>` - Merge a branch into current branch
- `git merge --no-ff <branch>` - Create merge commit even if fast-forward possible

### Reset Operations
- `git reset --soft HEAD~N` - Undo N commits, keep changes staged
- `git reset --mixed HEAD~N` (or `git reset HEAD~N`) - Undo N commits, keep changes unstaged
- `git reset --hard HEAD~N` - Undo N commits, discard all changes
- `git reset HEAD <file>` - Unstage a file
- `git reset --hard HEAD` - Discard all uncommitted changes
- `git reflog` - View history of HEAD positions (useful for recovery)

### Viewing History and Detached HEAD
- `git checkout <commit>` - View repository at a specific commit (detached HEAD)
- `git checkout HEAD~N` - Go back N commits
- `git checkout -` - Return to previous branch/commit
- `git checkout -b <branch>` - Create branch from current detached HEAD position
- `git switch <branch>` - Switch to a branch (modern alternative to checkout)
- `git switch -` - Return to previous branch
- `git bisect start/good/bad/reset` - Binary search through history to find bugs

### Revert Operations (Safely Undo Public Commits)
- `git revert HEAD` - Create commit that undoes the last commit
- `git revert <commit>` - Create commit that undoes a specific commit
- `git revert <commit1> <commit2>` - Revert multiple specific commits
- `git revert <oldest>..<newest>` - Revert a range of commits
- `git revert --no-commit HEAD` - Revert without auto-committing (stage changes)
- `git revert -m 1 <merge-commit>` - Revert a merge commit (keep first parent)
- `git revert --continue` - Continue after resolving conflicts
- `git revert --abort` - Cancel the revert operation

### Advanced Commands
- `git rebase <branch>` - Rebase current branch onto another
- `git rebase -i <commit>` - Interactive rebase for editing commit history
- `git stash` - Save uncommitted changes temporarily
- `git stash pop` - Restore and remove most recent stash
- `git stash list` - List all stashes
- `git cherry-pick <commit>` - Apply a specific commit to current branch

## Best Practices

1. **Commit often** - Make small, logical commits with clear messages
2. **Pull before you push** - Always sync with remote before pushing
3. **Use branches** - Never work directly on main for features
4. **Write descriptive commit messages** - Future you will be grateful
5. **Review before committing** - Use `git diff` and `git status`
6. **Never rebase public history** - Only rebase local/unpushed commits
7. **Test before merging** - Ensure code works before merging to main
8. **Resolve conflicts carefully** - Understand both changes before deciding
9. **Return to a branch from detached HEAD** - Don't make commits without a branch
10. **Use checkout for viewing, reset for changing** - Checkout is safe exploration, reset rewrites history
11. **Use revert for public commits** - Never reset commits that others have pulled
12. **Reset = local, Revert = public** - Reset for unpushed work, revert for shared branches

## Troubleshooting Common Issues

**Accidentally committed to main instead of a feature branch:**
```bash
git branch feature-name  # Create branch at current commit
git reset --hard HEAD~1  # Reset main back one commit
git checkout feature-name  # Switch to the feature branch
```

**Need to undo last commit but keep changes staged:**
```bash
git reset --soft HEAD~1
```

**Need to undo last commit and keep changes unstaged:**
```bash
git reset HEAD~1
# Or explicitly: git reset --mixed HEAD~1
```

**Completely discard last commit and all changes:**
```bash
git reset --hard HEAD~1
```

**Completely discard all uncommitted local changes:**
```bash
git reset --hard HEAD
```

**Unstage a file (but keep the changes):**
```bash
git reset HEAD filename.txt
```

**Accidentally did a hard reset - recover the commit:**
```bash
git reflog  # Find the commit hash before reset
git reset --hard <commit-hash>  # Restore to that commit
```

**In detached HEAD state and want to go back:**
```bash
git checkout main  # Or whatever branch you were on
# Or: git checkout -  # Go to previous location
# Or: git switch main  # Modern command
```

**Made commits in detached HEAD and want to save them:**
```bash
git branch save-my-commits  # Create branch at current position
git checkout save-my-commits  # Switch to the new branch
# Or in one command: git checkout -b save-my-commits
```

**Want to view an old version without losing current work:**
```bash
# First, make sure current work is committed or stashed
git stash  # Save current changes
git checkout <old-commit>  # View old version
# ... explore ...
git checkout main  # Return to main
git stash pop  # Restore your work
```

**Lost track of which branch you were on:**
```bash
git reflog  # Shows recent HEAD movements
# Look for the branch name in the log
git checkout <branch-name>
```

**View what changed in a commit:**
```bash
git show <commit-hash>
```

**Find out which branch contains a commit:**
```bash
git branch --contains <commit-hash>
```

**Combine last 3 commits into one:**
```bash
git reset --soft HEAD~3
git commit -m "Combined commit message"
```

**Compare current code with an old version:**
```bash
# Checkout the old version
git checkout <old-commit>
# In another terminal or after noting the differences
git diff main  # See what changed since then
git checkout main  # Return to current
```

**Need to undo a commit that was already pushed:**
```bash
# DON'T use reset for pushed commits
# Use revert instead
git revert <commit-hash>
git push origin main
```

**Revert the last 3 commits as one revert commit:**
```bash
git revert --no-commit HEAD~2..HEAD
git commit -m "Revert last 3 commits: reason for revert"
git push origin main
```

**Revert a merge commit:**
```bash
git revert -m 1 <merge-commit-hash>
# -m 1 keeps the first parent (usually main branch)
git push origin main
```

**Revert caused conflicts:**
```bash
# Resolve conflicts in the affected files
git add <resolved-files>
git revert --continue

# Or abort the revert
git revert --abort
```

**Need to re-apply a reverted commit:**
```bash
# Find the revert commit
git log --oneline
# Revert the revert
git revert <revert-commit-hash>
```

---

## Next Steps

After completing this exercise, you should:
- Practice these workflows on a real project
- Explore Git GUI tools (GitKraken, SourceTree, GitHub Desktop)
- Learn about Git hooks for automation
- Study advanced topics: git bisect, git reflog, git submodules
- Read about GitFlow or other branching strategies

Happy Git-ing! 🚀
