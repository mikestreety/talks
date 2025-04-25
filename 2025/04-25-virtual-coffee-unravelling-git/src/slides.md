---
titleTemplate: '%s - Unravelling Git: The secrets of the commit hash - Mike Street'
author: Mike Street
presenter: dev
browserExporter: dev
routerMode: hash

twoslash: false
contextMenu: false
monaco: false
favicon: 'https://www.mikestreety.co.uk/assets/img/favicon-512.png'

transition: slide-left
mdc: true
drawings:
  enabled: false
theme: the-unnamed
themeConfig:
  default-font-size: 1.4em
  code-font-size: 1.6em

layout: cover
---

# Unravelling Git
## The secrets of the commit hash

---
layout: about-me

helloMsg: Mike Street
name: "@mikestreety"
imageSrc: /images/mike.jpg
position: left
job: CTO & Lead Developer at Liquid Light
line1: "Cyclist & side-project-aholic"
line2: "Lover of processes"
social1: "@mikestreety on everything"
social2: mikestreety.co.uk
---

<!--

- Company of 15, look after over 60 websites
  - For us, processes are a must
- VC member for 2 years
- Process & communication are key
  - If something can be optimised, i'll find a way
  - If it's broken, take it apart and find out why. If it works, take it apart and find out why
- You can find me @mikestreety on pretty much every social network (including VC)
-->

---

# **Unravelling Git**: The secrets of the commit hash
## What are we going to cover?

<v-clicks>

-  Touch on what Git is
-  Overview of hashing
-  Look at the Git commit hash
-  Understand more about amending commits, rebasing & cherry-picking
-  📖 House rules - add context

</v-clicks>

<!--
-  Very quick overview of git
-  Talk about hashing and what that does
-  Examine the git commit hash
-  I cover the rules we have at Liquid Light to try and help add context
-->

---

# But why?

- A better understanding
- Anticipate conflicts

<v-click>

## ⚠️ This is a light-touch overview

</v-click>


<!--
- Interested me - was intrigued about the hash how it was made and what it
- How it worked
- Wanted to understand how pushing and pulling conflicts
- Worth noting this is very light touch - no inner workings
-->

---
layout: section
---

# Git

What is it? How does it work?

<!--
First things first, let's talk about Git
-->

---

# What is Git?

<style>
  blockquote {margin-bottom: 2em}
</style>

<v-switch>
<template #1>

> Git is a distributed, file-based version control system

But what does this mean?

</template>
<template #2>

> Git is a distributed, file-based **version control** system

### **Version control:**

Keeps track of changes in the filesystem

- Like a Google Docs history

</template>
<template #3>

> Git is a distributed, **file-based** version control system


### **File-based:**

Repository & config live in a `.git` folder

- Investigate the folder
- Copy and paste the folder (if on a different computer)
- No services or containers
- Branch names & tags are like domain names - `.git/refs/heads/[branch name]`

</template>
<template #4>

> Git is a **distributed**, file-based version control system


### **Distributed:**

Meaning there is no primary/main version - they are all equal

- Every repository is the same
- Github is not Git (although Pull Requests are cool)

</template>
</v-switch>

<!--
- Git is a way of keeping track of changes in your filesystem
- Version control
  - Keep track of changes & attribute them to people
  - Gives context & reasoning behind changes (if commits are good)
- File based
  - The git folder is there for you to look at, move and delete.
  -  Because it is file-based, you can take it with you
  -  No magical services
- Distributed
  - You push and pull the same to any repository
  - Although it is distributed, GitHub, Gitlab and Bitbucket have conditioned us to have a primary
-->

---
layout: section
---

# Hashing

---

# What is a hash?

> A hash is a fixed-size string or number derived from an input of any size, generated using a mathematical algorithm.

<v-click>

e.g.

Same input gives you the same (fixed sized) output every time.


![](/images/sha1-easy.webp){ width=500 }

</v-click>

<!--
- Fixed length (useful for data storage)
- Quick Comparisons
-->

---

# Hashing algorithms

Different algorithms exist with different speeds and complexities

- MD5 (32 characters),
- SHA-1 (40 chars)
- SHA-2
- Argon2
- etc

<!--
- Difference is Iterations & speed & complexity
- **Technically** they are irreversible (although libraries exist of common words)
-->

---

# Hashing usage

Passwords, file or path checking including in the `integrity`

Used throughout the web.

```html
<script
  src="https://example.com/example-framework.js"
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
  crossorigin="anonymous"></script>
```

<!--
- Often used to check the integrity of a file (is it the same?) without having to compare the whole file
- Hashes are used for passwords - stored as hash
  - When you login, it doesn't decode the hash, it hashes again and compares

-->

---
layout: section
---

## Git + Hashing =
# The commit hash

---

# Git uses SHA-1

<small>\* Git might be using SHA-256 _soon_</small>

Git hash doesn't need to be secure, just avoid collisions

The chance of two different inputs generating the same hash output:

$1.46 \times 10^{48}$

*(octillion)*

<small>146,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000</small>

<!--
-  SHA-1 is considered insecure for passwords, but for integrity checking, it's fine
-  Remember, with Git it doesn't matter if 2 commits from different repositories have the same hash, only if **in the same repo**
-  If it does happen, there is no error checking and may corrupt the repo
-->

---

# Hash input

What is the input for a commit hash?

<v-clicks>

- Parent commit hash*
- Commit message
- Date & time
- Author
- File changes
- A little bit of magic ✨

</v-clicks>

<v-click>

*Merge commits have 2 parent commit hashes

</v-click>

<!--
-  All these things (plus a few more) go into making the commit hash
- Essentially when any of these change
- **If you change any of these, the hash will update** (even a single full stop in the commit message)
-->


---
layout: section
---

# Changing the commit hash

<!--
- THis is the crux, this is why we are here
- Going back to our hash inputs

-->

---

# We'll be covering

There are many ways to change a hash, but the common ones we're covering are:

<v-clicks>

1. `git commit --amend`
2. `git rebase`
3. `git cherry-pick`

</v-clicks>

<!--
- Going to dive into these three and see how the commit changes
- What it can do for you and why you might use them (or want the hash to change)
- Three common ways of a hash changing
-->
---

# Preface

This is an example Git log we'll be using

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph:
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

### `1-p0-band`

1. `1` - commit index
2. `p0` - parent commit index
3. `band` - a random 4 letter word (commit message & file changes)

<!--
-  Before we get started, this is the format
-  Commit number, followed by parent, followed by a 4 letter word
-  The word represents a hash of commit/date/author etc
-  This is for demonstration purposes only
-->

---
layout: section
---

## How to change a commit hash

# 1. `git commit --amend`

---

# Amend

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

<v-click>

```bash
git commit --amend
```

</v-click>

<!--
- So the first way a hash might change is with an amend
- Fixing a typo, changing a file or similar
-->

---

# Amend

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
  gitGraph
    commit id: "0-p-face"
    commit id: "1-p0-band"
    commit id: "2-p1-form"
    commit id: "3-p2-king"
    commit id: "4-p3-book"
    commit id: "5-p4-able" type: HIGHLIGHT
```

<!--
-  And then your branch looks like this - note the "hash" has changed from "jump" to "able"
-  That's fine, you can push if you are happy
-->


---

# Amend a pushed commit

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

<v-click>

```bash
$ git push origin main
```

</v-click>

<!--
- Back to our original , bit this time we push up to origin
-->

---

# Amend a pushed commit

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
%%{init: {'themeVariables': {'git0': '#cb5dff'},'gitGraph': {'mainBranchName': 'origin/main'}} }%%
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

<v-click>

```bash
$ git commit --amend
```

</v-click>

<!--
- All good,
- But now we amend a local commit
-->

---

# Amend a pushed commit

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-able" type: HIGHLIGHT
```

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
%%{init: {'themeVariables': {'git0': '#cb5dff'},'gitGraph': {'mainBranchName': 'origin/main'}} }%%
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  commit id: "5-p4-jump"
```

<v-click>

```bash
$ git push origin main
```

</v-click>


<!--
- Suddenly the are different hashes.
- If we now want to push we get face with an error
-->

---

# When we try to push

And we are faced with this

```shell
! [rejected]      main -> main (non-fast-forward)
error: failed to push some refs to 'github.com:repo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. If you want to integrate the remote changes,
hint: use 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Git thinks there are commits which need to be pulled down


<!--
- This happens when there are commit hashes on the remote which aren't on yours
- Git can only resolve merges locally (depending on your strategy) so wants you to merge
- However, it's your commit
-->

---

# Different commit hashes

Git keeps track of branches: `.git/refs/heads/main`

Including remote ones: `.git/refs/remotes/origin/main`

Git sees they are different and panics 🚨

How do you solve it?

<v-click>

## It depends

</v-click>

<!--

- Git doesn't know they are the same commit but amended
- As the amend could be changing it COMPLETELY
-->

---

# Solving a rejected push

## 1. Pull down the changes

If the changes on remote are what you need, then pull them

They might be a colleague's changes

👍🏻 **Straight-forward, git flow, happy path**

<!--

- There are two ways of solving it
- Git really wants you to pull down the changes and merge them
-->

---

# Solving a rejected push

## 2. Force push

Overwrites everything on remote

Might affect CI, deployments or other things tied to commits

🐲 **Dangerous, says "my way or the highway", might be dragons**

---

# Solving a rejected push

## 2. Force push

### `--force`

- `⁠--force`: Overwrites changes on the remote branch without checking for others' updates. Risky!
- `⁠--force-with-lease`: Overwrites only if no one else has pushed changes after your last pull. Safer option!

<!--

- Or you can force push
-->

---
class: about-me
---

# 📖 Our in-house rules

- Never amened a pushed commit
- Never `--force` push main (or environment branches)
- Only `--force` push feature branches if you are the only one working on it

<!--
That;s the first one
-->

---
layout: section
---

## How to change a commit hash

# 2. `git rebase`

---


# Git branch

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
    commit id: "0-p-face"
    commit id: "1-p0-band"
    branch develop
    checkout develop
    commit id: "2-p1-form"
    commit id: "3-p2-king"
    commit id: "4-p3-book"
    checkout main
    commit id: "2-p1-jump"
```
<!--
- So this is a standard git branch method
- Branched off band, but jump has since been committed on main
-->

---
layout: center
---

# Updating develop with main

What if we want to update our develop branch with what is on main?

---

# Updating develop with main

- We could merge `main` into `develop` (merge commit)
- We could `cherry-pick` (more in a minute)
- Or we could `rebase`

<!--

- So how can we get jump into the develop branch?
- **Merge**, but then you get the "merged main into develop" commit
- **Cherry-pick**, seems simple, we'll cover in a minute, complex with lots of commits
- **Rebase**, let's explore rebasing
-->

---

# Git merge

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  branch develop
  checkout develop
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  checkout main
  commit id: "2-p1-jump"
  checkout develop
  merge main id: "6-p5-p4"
```

<!--
- **Merge**, but then you get the "merged main into develop" commit
- The expected path is to merge develop into main
- This creates a merge commit
-->

---
layout: center
---

# Enter the rebase

Reapply commits on top of another base tip

---


# Prepare to rebase

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  branch develop
  checkout develop
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  checkout main
  commit id: "2-p1-jump"
```

<!--
- Going back to our current state
- What we want is our branch to actually be based off "jump"
-->

---

# What we hope will happen

```bash{1|2|1-2}
$ git checkout develop
$ git rebase main
```

<v-click at="2">

## Ideally...

```mermaid {theme: 'base', scale: 1.3, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-jump"
  branch develop
  checkout develop
  commit id: "2-p1-form"
  commit id: "3-p2-king"
  commit id: "4-p3-book"
  checkout main
```
</v-click>

<v-click>

However, this isn't what would happen

</v-click>

<!--
- This is our desired outcome
- Jump moves back to the beginning and our commits follow
- however
-->

---

# Hash input

What is the input for a commit hash?

- **Parent commit hash**
- Commit message
- Date & time
- Author
- File changes
- A little bit of magic ✨

<!--
- We know from earlier the parent is part of the hash
- So actually the new graph looks like
-->

---

# We have rebased

<v-switch>
<template #1>

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-jump"
  branch develop
  checkout develop
```

</template>
<template #2>

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-jump"
  branch develop
  checkout develop
  commit id: "3-p5-form"
```

</template>

<template #3>

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-jump"
  branch develop
  checkout develop
  commit id: "3-p5-form"
  commit id: "4-p6-king"
```

</template>

<template #4>

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
  commit id: "0-p-face"
  commit id: "1-p0-band"
  commit id: "2-p1-jump"
  branch develop
  checkout develop
  commit id: "3-p5-form"
  commit id: "4-p6-king"
  commit id: "5-p7-book"
```

</template>
</v-switch>

<!-- notes:
- It rewinds to main and replays each commit on top of each other
- Notice the parent indexes have now changed

# Different commit hash

## Parent commit & date

- Worth noting the commit date & author would change
- This would all be fine, unless you have pushed your commit before rebasing
-->

---
layout: center
---

# Re-commit details

Git commits have 2 authors and 2 dates

**Author date**: This is the date when the changes were originally made.

**Commit date**: This date reflects when the changes were last applied or altered in any significant way

`git log --pretty=fuller`

![](/images/author-and-commit-date.png){ width=400px }

---

# Trying to push

```bash
$ git push origin develop
```

<v-click>

```shell
! [rejected]      develop -> develop (non-fast-forward)
error: failed to push some refs to 'github.com:repo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. If you want to integrate the remote changes,
hint: use 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

</v-click>
<v-click>

```
git push origin develop --force
```

</v-click>

<!--
- They are different (as git thinks)
- However, we are on our feature branch so we can force push
-->

---
class: about-me
---

# 📖 Our in-house rules

- Never amened a pushed commit
- Never `--force` push main (or environment branches)
- Only `--force` push feature branches if you are the only one working on it

<v-click>

- Use `rebase` to update feature branches with `main` (before merging)
- Use `merge` to update our main branch

</v-click>

---
layout: section
---

## How to change a commit hash

# 3. `git cherry-pick`

---

# Our branches

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
	commit id: "3-p2-folk"
	commit id: "4-p3-king"
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
```
<!--
- Back to our commit graph
-->

---

# Identifying the target

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
	commit id: "3-p2-folk"
	commit id: "4-p3-king" type: HIGHLIGHT
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
```

<!--
- Say we'd done a nice atomic commit on a branch, but that particular fix/feature was wanted on main
- The commit we want is `3-p2-king`
- If we wanted it on main we could `git cherry pick`
-->

---

# Cherry pick the commit

```bash{1|2}
$ git checkout main
$ git cherry-pick 3-p2-king
```

---

# What we expect

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
  commit id: "3-p2-folk"
	commit id: "4-p3-king"
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
	cherry-pick id: "4-p3-king"
```
<!--
- This is what you think it would look like
- This is also what a lot of tutorials make it out to be
- That it has picked it up and dropped it on main
-->

---

# What it actually looks like

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
	commit id: "3-p2-folk"
	commit id: "4-p3-king"
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
	commit id: "3-p5-king"
```
<!--
- However, it takes the contents of the commit and re-commits it
- Just like a rebase, the commit gets an updated commit author & date
- Which means, it gets a new hash
-->

---

# Duplicate commits

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
	commit id: "3-p2-folk"
	commit id: "4-p3-king" type: HIGHLIGHT
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
	commit id: "6-p5-king" type: HIGHLIGHT
```
<!--
- This means we now have 2 commits with the same work on
- Git _might_ be able to resolve but will probably conflict when you merge
-->

---

# Eliminate the commit

```mermaid {theme: 'base', scale: 1.7, themeVariables: {git0: '#44ffd2', git1: '#f141a8', darkMode: false, background: '#161c2c', commitLabelColor: '#000', commitLabelBackground: '#f3eff5', commitLabelFontSize: '13px', tagLabelColor: '#fe4a49', gitInv0: '#fe4a49', gitInv1: '#fe4a49', tertiaryColor: '#f00'}}
gitGraph
	commit id: "0-p-face"
	commit id: "1-p0-band"
	branch develop
	checkout develop
	commit id: "2-p1-form"
	commit id: "3-p2-folk"
	commit id: "4-p3-king" type: REVERSE
	commit id: "5-p4-book"
	checkout main
	commit id: "2-p1-jump"
	commit id: "6-p5-king"
```
<!--
- So we need to no remove the "king" commit from our develop branch
-->

---

# Removing a commit

To remove the king commit from the develop branch we can:

- Interactive rebase: `git rebase HEAD~3 -i`
  - `git rebase main`
- Create a new branch off `2-p1-form` and cherry-pick `4-p3-book`
- Revert `3-p2-king` on the develop branch
- Just merge and deal with the conflicts

<v-click>

## It depends

</v-click>

---
class: about-me
---

# 📖 Our in-house rules

- Never amened a pushed commit
- Never `--force` push main (or environment branches)
- Only `--force` push feature branches if you are the only one working on it
- Use `rebase` to update feature branches with `main` (before merging)
- Use `merge` to update our main branch

<v-click>

- Avoid cherry picks
- Consider squashing or `git rebase -i` before merging

</v-click>

---

# Commit hash input

- Parent commit hash(es)
- Commit message
- Commit Date & time
- Commit user
- Authored date & time
- Author
- File changes
- A little bit of magic ✨

---
layout: about-me
helloMsg: Thank you
imageSrc: /images/qr.png
name: "@mikestreety"
position: right
job: www.mikestreety.co.uk
---

<style>
.slidev-page-53 div[style] {
  background-size: contain !important;
  background-size: 75% !important;
}
</style>
