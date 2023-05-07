---
title: How to Build a GitHub Pages website using jekyll and Chirpy
date: 2023-05-07 12:00:00 -500
categories: [GitHub.io,Jekyll]
tags: [Website,Jekyll,GitHub.io,Favicon]
---

# Pre-Stagging Build Environment

There are several tools needed to build a GitHub pages site using jekyll themes. The following list are things I believe need to be understood and installed prior to building the site. 

1- VSCode installation
 1- A Path setup to build the environment in locally. 
1- A GitHub account
1- Installation of Obsidian (Not Necessary for Build but is a big part of easy blog creation and note taking.)
1- Git installed

Once those items are installed you should be well setup to begin rolling out your GitHub pages website using Chirpy in no time. 

---

# Getting Chirpy linked to GitHub.io


Go to the Chirpy Github page and follow the instructions in the readme. 

https://github.com/cotes2020/jekyll-theme-chirpy#documentation

In the ReadMe there is a link to the Wiki pages that you will need to follow to install this build. 

https://github.com/cotes2020/jekyll-theme-chirpy/wiki

The first step is to make sure you have the initial setup to install Jekyll. First we need to install ruby and Jekyll.

Follow these guides from the Jekyllrb.com site.

https://jekyllrb.com/docs/installation/

I'm using windows for this build but Options are available for all OS's
https://jekyllrb.com/docs/installation/windows/

Once you have installed Ruby and Jekyll its now time to begin creating the site. 

I went with the Chirpy starter method bc I wanted to have freedom of breaking and not realying on maintaining code with the Chirpy folks. 

Scroll down to Prerequisites and double check that you have completted these items. Once confirmed preq's move to Installations and begin to create the clone of the Chirpy starter using there template. Click the use the template link in the below README.md

https://github.com/cotes2020/chirpy-starter#installation

You must have a GitHub account, and Git installed so we can pull down the remote repo after its created. 

![[Pasted image 20230507112805.png]]

Make the Repo Public and Create repo from template.

Navigate to the repo and click into the repo.

Once we build the GitHub pages site template files we will want to pull that repo down to our local machine by cloning it.  We can clone the site using GIT, VScode and a simple git clone command.

Navigate to the Code drop down and copy the Repo URL.

![[Pasted image 20230507113038.png]]

Now navigate to your VSCode instance and open your Code folder.

If you dont have a folder created already I recommend using the Terminal in VScode to mkdir.

![[Pasted image 20230507113340.png]]

navigate to your C drive or whatever Drive you store Code. 

	mkdir Prog

Now access and create a workspace for that folder from the start menu in VScode or by using the terminal. 

Use Open Folder and find the Prog folder.
![[Pasted image 20230507113604.png]]

Or in the Terminal 


	cd Prog
	pwd

Do print working directory, "pwd" to confirm you in the right folder. Now we are ready to clone the template to our local environment. 

Copy the URL if needed again from the Code dropdown of your GitHub repo main directory and run the following commands.

	git clone <the url to your remote repo>

This will clone the template to your local instance. 

now cd into your github pages chirpy site and then open a new workspace in VScode

	cd <your GitHub pages local repo>
	code . 

once in the new GitHub pages local repo you are now good to pull down the dependencies using Ruby bundle commands so the Jekyll site can build itself in a moment. Simply run bundle form the terminal. 


	bundle

Then load the site with 

	bundle exec jekyll server 
 
 or 
 
	bundle exec jekyll s

It will serve out the webserver using localhost address on port 4000

	http://127.0.0.1:4000/

Great work your playground is now built. Now its time to break things. 


# Customizing Your GitHub Pages Site

One quick statement here that needs be addressed is, you dont have to use Chirpy but I highly recommend you stick with it for this build and then explore other themes once you have built this site out for yourself. Many refrences and videos online refer to being able to make a GitHub pages repo and use the theme builder button that use to be backed into the settings > pages > Build and Deployment section. This was depreciated in 2022 for security concerns. 

A list of Jekyll themes can be found here:

	https://jekyllrb.com/docs/themes/

### Modifying \_config.yml

I followed the amazing guide by Techno Tim for a good portion of my initial build. Check out the following video especially the customizing your site section of the video Linked below.

[Meet Jekyll - The Static Site Generator - YouTube](https://www.youtube.com/watch?v=F8iOU1ci19Q&t=248s)
- Meet Jekyll - The Static Site Generator
  
If want to make global config changes do so in the config yml file. 

Items you should change first off:

- Timezone
- Title
- Tagline
- Description
- URL to your GitHub Pages Site
- Github username
- Twitter username
- Uncomment linkedin and configure this. (More on this later.)
- Avatar 

For the avatar you can upload a file and commit this to configure your avatar properly using GitHub CDN instead of standing one up like Azure CDN or by paying to use another service. 

What I did to be able to upload images into my posts and into the avatar:

1- Create a folder at the root directory of the site called images.

	pwd
	cd <Site Repo if not already in it>
	mkdir images

Find image online and download that image. Recommend signing up for Adobe Stock bc the image quality is best in biz. 

https://stock.adobe.com/

Open another terminal window another terminal cd to downloads

![[Pasted image 20230507123105.png]]

Type cd to get to the system root directory

	cd
	cd Downloads

In another terminal window now 3 open in total navigate to the images directory and pwd

	cd images
	pwd

Copy the path to images and move back to the downloads folder window. 

	cp image.png <path you copied to images/>/images.png

The cp command will copy the image in the downloads folder to your workspace images directory.  

Now commit the changes to your remote repo using GitHub commands from the terminal or by using VScode source control.

![[Pasted image 20230507123639.png]]

![[Pasted image 20230507123706.png]]

yes

![[Pasted image 20230507123713.png]]

