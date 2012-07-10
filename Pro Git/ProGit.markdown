# Chapter 1 Getting Started

* VCS: Local, Centralized, Distributed

## Git Basics

* Snapshots: 
  * not delta update
  * a set of snapshots of mini filesystem
  * file changed: make a new snapshot; unchanged, a link to previous identical file. 

* Most operations local, no network needed (unlike CVCS)

* Integrity:
  * check-summed before stored by using SHA-1 hash. 

* Geneerally only **adds** data

* Three States: (Pay Attention)
  1. **commited**: data safely stored locally. 
  2. **modified**: have changed the file but not commited to database yet.
  3. **staged**: you have already marked it in its current version to go into next commit snapshot

* Three main sections:
  1. Working Directory
  2. Staging Area
  3. git directory

## Chapter 2 Git Basics

* Initialize a git repo: ``git init``
* Clone: ``git clone addr/to/git.git`` (Protocol: http(s), SSH, git)
* Record changes:
  * tracked: unmodified, modified or staged
  * untracked: everything else - not in last snapshot and not in staging are
  * to track files: ``git add file``
  * use ``git ignore`` to ignore files. record them in ``.gitignore``
    * blank lines ignored, # to comment
    * glob patterens work: *, [abc], ?, [0-9]
    * end patterns with ``/`` to indicate a directory
    * negate pattern by starting with ``!``: with ``*.cc``, ``!lib.cc`` will still be recorded. 
  * use ``git diff`` to show detailed changes in code:
    * to see what you've changed but not yet staged: ``git diff`` [Only changes that are still unstaged: confusing! will give you no output if you've staged all the changes]
    * to see what you've staged and that will go into next commit: ``git diff --staged``

  * ``git -a`` automatically stage every file that's already tracked before doing the commit
  * Remove:
    * ``git rm``: remove from tracked file, remove from working dir. Use ``-f`` if you've added to the staged area already: a safety net preventing removal of files that haven't yet been recorded in snapshot
    * ``rm``: changed but not updated, i.e., *unstaged*. So file will still be in git, run ``git rm`` to remove the file in git. 
    * ``git rm --cached``: keep file in hard drive but don't want git to track it any more, useful for mistakenly added logged files etc. 
  * Rename:
    * ``git mv from_file to_file``
    * Equivalent to: 
    ``
      mv from_file to_file
      git rm from_file
      git add to_file
    ``
  * Log:
    * ``git log``: -p to show diff in each commit, -2 to limit 2 recent, --stat to show abbreviated line change stat, --pretty=short, full, fuller, oneline, format:"%h - %.."
    * --since=2.weeks, --until, --after, --before, --author, --committer

* Undo: **ATTENTION, may lose info if done wrong**
  1. ``git commit --amend``:
    * commit, add forgotten file, run this command, overwrites prev commit. 
  2. ``git reset HEAD <file>``:
    * unstaged a staged file
  3. ``git checkout -- <file>``:
    * discard changes in working directory (by checking out the old version)
    * DANGEROUS: any changes made are unstaged hence not recoverable. 

* Remote: 
  1. ``git remote add [shortname] [url]``
  2. ``git fetch``: get all info that someone has but you don't yet. **NOTE**: this command does not merge.
  3. ``git push [remote] [brance]``
  4. ``git remote show [remote]``
  5. ``git remote rename [old] [new]``
  6. ``git tag`` to show release points
    * ``git tag -a v1.4 -m 'version 1.4'``: to create annotated tag.
    * ``git tag -s v1.5 -m 'version 1.5'``: to created signed tags with GPG. 
    * ``git tag -v v1.5``: to verify a signature, signer's public key needed
    * ``git tag v1.4-lw``: create lightweight tags. 
    * ``git tag -a v1.2 [checksum]``: tag file later
    * tags don't get pushed by default. push origin v1.5 explicitly or git push origin --tags

## Chapter 3 Git Branching

* a commit object point to a tree object that contains blob objects, which represent contents of each file. 
* subsequent commit stores a ptr to immediate previous commit obj 
* branch:
  * a lightweight ptr that points to a commit. 

* ``git branch [new branch name]``: create new branch (HEAD doesn't change)

* a special ptr called ``HEAD`` points to the branch I'm on. 
  * ``git checkout testing``: HEAD now points to branch testing

* only the branch that ``HEAD`` points to will automatically move forward

### Basic Branching and Merging

#### Local branching

* ``git checkout -b [new branch]``: create a new branch and switch to it at same time. 
* **Fast Forward** merge: merge one commit with a commit that can be reached by following history, then git simply moves the pointer forward. 
  1. master. 
  2. git branch hotfix. 
  3. git checkout master
  4. git merge hotfix
* ``git branch -d [branch name]``: delete a branch. 

#### Local Merging:
  1. checkout branch i wish to merge into. 
  2. ``git merge``: create a new commit object with merged work:
    * if C1 and C2 in different branch, ``git merge`` figures out best common ancestor, create a new version C3 that contains both of their work
  3. ``git branch -d [old brance]``: clean up branches.

* What if cannot automatic merge? 
  * same file get different changes among two branches. 
  * solution: 
    1. open files, manually delete conflicts. 
      * everything between ===== <<<<<  >>>>>: choose one side
      * or use ``git mergetool``: asks you if successful, if yes, stage it. 
    2. ``git add`` to mark it as resolved.
    3. Everything's fine, run ``git commit`` again to finalize merge commit. 

* ``git branch --merged`` to see branches that are already merged:
  * generally safe to delete those without asterisk (*, indicate checkout branch) is safe to delete
  * if it's not merged yet, will alarm you. use -D to confirm deletion. 

* Good practice:
  1. ``master`` only contain entirely stable code. 
  2. introduce multiple long-running branches in large, complex projects: master, develop, topic.. 

#### Remote Branching

* references to state of branches on remote repo
* update when communicate with network

* Pushing:
  * ``git push origin serverfix``: automatically expands to ``refs/heads/serverfix:refs/heads/serverfix``
  * ``git push origin serverfix:awesomebranch``: push to local branch to some branch that named differently
  * ``git fecth`` don't give you local editable copies. 
    * ``git merge origin/serverfix`` to merge into my local working branch
    * or ``git checkout -b serverfix origin/serverfix`` to base it off the remote branch

* **Tracking Branch**
  * checking out a local branch from a remote branch creates a tracking branch
  * has direct relationship to the remote branch. git push and pull all pointed to that branch. 
    * when ``git clone``, create master branch that tracks origin/master








