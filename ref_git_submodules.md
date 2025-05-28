# Setup to reuse, modify multiple open source libraries/repositories for your project using submodules
### 1. Initialize Your Project Git Repo  

If not done already:  
``` bash
mkdir your_project
cd your_project
git init
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
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

### 3a. Commit Submodule Setup
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
``` bash
touch MODIFICATIONS.md
```

``` markdown
### Modifications to library X
- Renamed `config.py` → `my_config.py`
- Added `error_handler.py`
```


### 3b. Push your project to GitHub

```bash
git status                   # See what’s changed
git add .                    # Stage all changes
git commit -m "Initial project with third-party libraries"
git remote add origin https://github.com/yourname/your_project.git         # For first time only
git branch -M main                                                         # For first time only
git push -u origin main
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

