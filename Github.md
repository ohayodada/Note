# Github

## Create a project folder
```shell
$ mkdir ~/myproject
$ cd ~/myproject
```
## To initialize a git repository in the folder

```shell
$ git init
```

## Create a file

```shell
$ touch test.txt
```

## To see which files git knows exist

```shell
$ git status
```
## Add a new file to the repo 

```shell
$ git add test.txt
```

## Create a commit

```shell
$ git commit -m "This is my commit"
```

## Create a new branch

```she;;
$ git branch
```

## Create a new repository on GitHub
- To create a new repo on [GitHub](https://github.com)
- Coneect project to gihub 
```shell
$ git remote add origin http://github.com/name/repo.git
$ git push -u origin master
```

## Push a branch to GitHub
```shell
$ git push origin new_branch
```

