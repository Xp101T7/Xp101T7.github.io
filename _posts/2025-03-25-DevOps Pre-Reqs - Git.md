---
title: DevOps Pre-Reqs - Git
date: 2025-03-25 12:00:00 -500
categories: [Devops,GIT]
tags: [Developer,DEVOPS,GITHUB,GIT]

---

> [!quote] Learn all you can from the mistakes of others. You won't have time to make them all yourself.
> — Alfred Sheinwold

---

## DevOps Pre-Reqs - Git

Install GIT

```JSON
sudo yum install git
```

```JSON
git version
```

Initialize a GIT Repository, you must be inside the repo to init it.

```JSON
git init repo1
```

Config files to track

```JSON
git add file1 file2
```

Check the status of the tracked files 
```JSON
git status 
```

Git tracks changes, Stage Changes

```JSON
git add file3
```

Commit Changes

```JSON
git commit -m "Init Commit"
```

### Remote Repositories

Local Git Repo 

```JSON
git init
git add .
git commit -m "mit it"
```

Create remote repo inside github then clone it down using git clone or use remote add you name the repository localy

```JSON
git remote add repofolder https://github.com/your/repo
```

```JSON
git push repofolder main
```

Cloning 

```JSON
git clone url
```

Get the remote repos

```JSON
git remote -v
```

If you need to sync changes from other sources that have been merged into main or another branch use git pull

```JSON
git pull
```

