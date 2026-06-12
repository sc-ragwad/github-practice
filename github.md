# Git and Github Revision Documentation

### 1. Setup
- Downloaded and set up using the git installer from `https://git-scm.com/install/windows`

- configured username and email using git config

---

### 2. Initialization

- initializing the directory
```shell
git init
```
- checking the status
```shell
git status #md file displays in red
```
- adding the files to staging area
```shell
git add . # git add . adds all the files
# can use `git add file_name` to add only a specific file
```
- checking the status again
```shell
git status #the md file is added to staging area and has turned green
```
- commiting the file
```shell
git commit -m "first commit"
```
---

### 3. Working with Repositories
- Access a repository by cloning one or creating a new one
```shell
git clone url
```
- best practice is to make a new branch to make changes in
```shell
git checkout -b branch_name
```
- to get a list of all the present branches
```shell
git branch
```
- switch branches
```shell
git switch branch_name
```
