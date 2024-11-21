---
title: Initialiaze Git Repo and Set Obsidian Git Auto Backup on already
date: 2023-05-06 12:00:00 -500
categories: [github,vscode]
tags: [Red,Blue,Purple]
---



[Initialize a Git Repo in VSCode: Tips on Configuring Your Default Branch and Adding a GitIgnore - YouTube](https://www.youtube.com/watch?v=Ik3wFSyFaNc)


https://www.learnhowtoprogram.com/intermediate-javascript/test-driven-development-and-environments-with-javascript/git-best-practices-and-adding-a-gitignore-file

Commit .gitignore first

```bash
git add .gitignore  
git commit -m "add .gitignore file to project" 
git branch -M main
git remote add origin <repo http link>
git pull origin main --allow-unrelated-histories
git push -u origin main

cp file.json C:\Users\user\Prog\stash\file.json
git checkout HEAD file.json

```

[Git Create New Remote Branch From Local Repository And Push to GitHub (first time from command line) - YouTube](https://www.youtube.com/watch?v=8BxwhSrWo1s)