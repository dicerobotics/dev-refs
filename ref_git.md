# Basic git commands
Reference: https://education.github.com/git-cheat-sheet-education.pdf
### Setup
Configuring user information used across all local repositories

``` shell
git config --global user.name “<firstname lastname>”
# set a name that is identifiable for credit when review version history
git config --global user.email “<valid-email>”
# set an email address that will be associated with each history marker
git config --global color.ui auto
# set automatic command line coloring for Git for easy reviewing
```

### Setup & Init
Configuring user information, initializing and cloning repositories
``` shell
git init
# initialize an existing directory as a Git repository
git clone [url]
# retrieve an entire repository from a hosted location via URL
```

### Stage & Snapshot
Working with snapshots and the Git staging area
``` shell
git status
# show modified files in working directory, staged for your next commit
git add <file>
# add a file as it looks now to your next commit (stage)
git reset <file>
# unstage a file while retaining the changes in working directory
git diff
# diff of what is changed but not staged
git diff --staged
# diff of what is staged but not yet commited
git commit -m “<descriptive message>”
# commit your staged content as a new commit snapshot
```

### Branch & Merge
Isolating work in branches, changing context, and integrating changes
``` shell
git branch
# list your branches. a * will appear next to the currently active branch
git branch <branch-name>
# create a new branch at the current commit
git checkout
# switch to another branch and check it out into your working directory
git merge <branch>
# merge the specified branch’s history into the current one
git log
# show all commits in the current branch’s history
```

### Inspect & Compare
Examining logs, diffs and object information

``` shell
git log
# show the commit history for the currently active branch
git log branchB..branchA
# show the commits on branchA that are not on branchB
git log --follow <file>
# show the commits that changed file, even across renames
git diff branchB...branchA
# show the diff of what is in branchA that is not in branchB
git show <SHA>
# show any object in Git in human-readable format
```

### Tracking Path Changes
Versioning file removes and path changes
``` shell
git rm <file>
# delete the file from project and stage the removal for commit
git mv <existing-path> <new-path>
# change an existing file path and stage the move
git log --stat -M
# show all commit logs with indication of any paths that moved
```

### Ignoring Patterns
Preventing unintentional staging or commiting of files
``` shell
logs/
*.notes
pattern*/
# Save a file with desired paterns as .gitignore with either direct string matches or wildcard globs.

git config --global core.excludesfile <file>
# system wide ignore patern for all local repositories
```

### Share & Update
Retrieving updates from another repository and updating local repos
``` shell
git remote add <alias> <url>
# add a git URL as an alias
git fetch <alias>
# fetch down all the branches from that Git remote
git merge <alias>/<branch>
# merge a remote branch into your current branch to bring it up to date
git push <alias> <branch>
# Transmit local branch commits to the remote repository branch
git pull
# fetch and merge any commits from the tracking remote branch
```


### Rewrite History
Rewriting branches, updating commits and clearing history
``` shell
git rebase <branch]>
# apply any commits of current branch ahead of specified one
git reset --hard <commit>
# clear staging area, rewrite working tree from specified commit
```

### Temporary Commits
Temporarily store modified, tracked files in order to change branches
``` shell
git stash
# Save modified and staged changes
git stash list
# list stack-order of stashed file changes
git stash pop
# write working from top of stash stack
git stash drop
# discard the changes from top of stash stack
```


# Setup to modify an open source project and use to accomplish your own work
### 1. Fork the Original Repository
- If the project is on GitHub, and you plan to modify it:  
- Go to the original repo's GitHub page.
- Click the Fork button (top-right).

This creates a copy of the repo under your GitHub account.  
Use fork if you want to track changes from the original project or potentially contribute back.  

### 2. Clone Your Fork Locally
Download your fork to your machine:
```bash
git clone https://github.com/YOUR_USERNAME/REPO_NAME.git
cd REPO_NAME
```

### 3. Add the Original Repo as a Remote (Optional)
This helps you pull updates from the original project:
```bash
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO_NAME.git
```

Now you have:  
`origin`: your fork  
`upstream`: the original repo  

To pull in updates later:
```bash
git fetch upstream
git merge upstream/main
```
Caution: merging upstream/main may raise conflict with your modifications, if any.

### 4. Make Changes
Edit files, add your own code, and create new files as needed.  
You can also separate your work in a new branch:  
```bash
git checkout -b my-feature
```

### 5. Track & Commit Changes
```bash
git status                   # See what’s changed
git add .                    # Stage all changes
git commit -m "Add my feature / Modify XYZ"
```
### 6. Push Your Work to GitHub
```bash
git push origin my-feature
```
### 7. Publish Your Project (Optional)
If you’re building your own new project (even partially based on others):  
Create a new repo on GitHub  
Push your code to it:  

```bash
git remote set-url origin https://github.com/YOUR_USERNAME/NEW_PROJECT.git
git push -u origin main
```

### 8. Add License, README, and Attribution Files
Include:
`LICENSE` (your license)
`README.md`
`MODIFICATIONS.md` or `ACKNOWLEDGMENTS.md`
`NOTICE` if required

### 9. (Optional) Publish Releases
Go to the Releases tab on GitHub to tag stable versions (v1.0, etc.)


## Useful Commands Summary

| Purpose               | Command                                         |
| --------------------- | ----------------------------------------------- |
| Clone your fork       | `git clone https://github.com/YOU/REPO.git`     |
| Add original repo     | `git remote add upstream URL`                   |
| Pull original updates | `git fetch upstream && git merge upstream/main` |
| Create a new branch   | `git checkout -b my-branch`                     |
| Stage and commit      | `git add . && git commit -m "message"`          |
| Push changes          | `git push origin my-branch`                     |


### Best Practices
- Keep commits atomic and descriptive  
- Use branches for features/changes  
- Regularly pull from `upstream`  
- Document your changes (e.g., `MODIFICATIONS.md`)  
- Respect and follow license terms of any included code  


### Suggested Project Structure

```vbnet
your-project/
│
├── LICENSE                  ← Your project's license
├── README.md                ← Overview, usage, install instructions
├── MODIFICATIONS.md         ← What you changed in reused code
├── NOTICE                   ← License attributions and notices
├── .gitignore               ← Files to ignore in Git
├── requirements.txt         ← Python dependencies (if using Python)
│
├── src/                     ← Your original source code
│   └── ...
│
├── third_party/             ← Modified or reused open source code
│   └── library/
│       ├── LICENSE
│       ├── original_file.py
│       └── modified_file.py
│
├── tests/                   ← Unit or integration tests
│   └── test_example.py
│
└── scripts/                 ← Setup, data, or install scripts
    └── install.sh
```

# Setup to reuse, modify multiple open source project to accomplish your own work using submodules

### 1. Initialize Your Project Git Repo  

If not done already:  
``` bash
mkdir your_project
cd your_project
git init
```

### 2. Add a Third-Party Repo as a Submodule

```bash
git submodule add https://github.com/author1/lib1.git third_party/lib1  
```
Repeat for others:  
```bash
git submodule add https://github.com/author2/lib2.git third_party/lib2
```
This will:  
- Clone the repo into `third_party/libX/`   
- Track it via `.gitmodules`  

### 3. Commit Submodule Setup
``` bash
git commit -m "Add third-party libraries as submodules in third_party/"
```

Now your .gitmodules might look like:  

```ini
[submodule "third_party/lib1"]
  path = third_party/lib1
  url = https://github.com/author1/lib1.git

[submodule "third_party/lib2"]
  path = third_party/lib2
  url = https://github.com/author2/lib2.git
```

Now you can modify third-party code freely  

- Go into `third_party/libX/`
- Make changes, add new files
- You can even remove the .git folder to detach it permanently (not recommended though). Then Git will treat it as regular project code.
``` bash
rm -rf third_party/library/.git
```

Don't forget to track your Modifications

- Add a MODIFICATIONS.md in each third_party/libX/:

``` markdown
### Modifications to library X
- Renamed `config.py` → `my_config.py`
- Added `error_handler.py`
```

### 4. When Cloning Your Project Later
Use this to clone and initialize submodules:

```bash
git clone --recurse-submodules https://github.com/you/your_project.git
```
Or, if already cloned:

``` bash
git submodule update --init --recursive
```

### 5. Pulling Updates from Upstream Repos (Caution: Conflicts expected)
To update a specific third-party repo to its latest version:

``` bash
cd third_party/lib1
git checkout main         # Or whatever the default branch is
git pull origin main
cd ../../
git add third_party/lib1
git commit -m "Update lib1 submodule"
```
Caution: pulling, merging upstream/main may raise conflict with your modifications, if any.

### 6. If You Modify Submodule Code

- You can:  
    - Fork the original repo and point the submodule to your fork (`git remote set-url origin`)
    - Or **track changes locally**, and commit inside the submodule directory
- Either way, you can document changes in MODIFICATIONS.md

### 7. Optional: Lock to a Specific Commit

Sometimes you want to lock your project to a known stable version of a library.  

``` bash
cd third_party/lib1
git checkout <commit_hash>
cd ../../
git add third_party/lib1
git commit -m "Lock lib1 to specific commit"
```

### 8. Push your project to GitHub

```bash
git add .
git commit -m "Initial project with third-party libraries"
git remote add origin https://github.com/yourname/your_project.git
git push -u origin main
```
