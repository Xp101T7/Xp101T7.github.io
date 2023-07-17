---
title: How to Build a GitHub Pages website using Jekyll and Chirpy
date: 2023-05-07 12:00:00 -500
categories: [GitHub.io,Jekyll]
tags: [Website,Jekyll,GitHub.io,Favicon]
---

# Pre-Stagging Build Environment

There are several tools needed to build a GitHub pages site using jekyll themes. The following list are things I believe need to be understood and installed prior to building the site. 

1. VSCode installation 
2. A Path setup to build the environment in locally. 
3. A GitHub account 
4. Installation of Obsidian (Not Necessary for Build but is a big part of easy blog creation and note taking.)
5. Git installed

Once those items are installed you should be well setup to begin rolling out your GitHub pages website using Chirpy in no time. 

---

## Getting Chirpy linked to GitHub.io


Go to the Chirpy Github page and follow the instructions in the readme. 

[https://github.com/cotes2020/jekyll-theme-chirpy#documentation](https://github.com/cotes2020/jekyll-theme-chirpy#documentation)

In the ReadMe there is a link to the Wiki pages that you will need to follow to install this build. 

[https://github.com/cotes2020/jekyll-theme-chirpy/wiki](https://github.com/cotes2020/jekyll-theme-chirpy/wiki)

The first step is to make sure you have the initial setup to install Jekyll. First we need to install ruby and Jekyll.

Follow these guides from the Jekyllrb.com site.

[https://jekyllrb.com/docs/installation/](https://jekyllrb.com/docs/installation/)

I'm using windows for this build but Options are available for all OS's
[https://jekyllrb.com/docs/installation/windows/](https://jekyllrb.com/docs/installation/windows/)

Once you have installed Ruby and Jekyll its now time to begin creating the site. 

I went with the Chirpy starter method bc I wanted to have freedom of breaking and not realying on maintaining code with the Chirpy folks. 

Scroll down to Prerequisites and double check that you have completted these items. Once confirmed preq's move to Installations and begin to create the clone of the Chirpy starter using there template. Click the use the template link in the below README.md

[https://github.com/cotes2020/chirpy-starter#installation](https://github.com/cotes2020/chirpy-starter#installation)

You must have a GitHub account, and Git installed so we can pull down the remote repo after its created. 

![Pasted image 20230507112805.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507112805.png)

Make the Repo Public and Create repo from template.

Navigate to the repo and click into the repo.

Once we build the GitHub pages site template files we will want to pull that repo down to our local machine by cloning it.  We can clone the site using GIT, VScode and a simple git clone command.

Navigate to the Code drop down and copy the Repo URL.

![Pasted image 20230507113038.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507113038.png)

Now navigate to your VSCode instance and open your Code folder.

If you dont have a folder created already I recommend using the Terminal in VScode to mkdir.

![Pasted image 20230507113340.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507113340.png)

navigate to your C drive or whatever Drive you store Code. 

	mkdir Prog

Now access and create a workspace for that folder from the start menu in VScode or by using the terminal. 

Use Open Folder and find the Prog folder.

![Pasted image 20230507113604.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507113604.png)

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


## Customizing Your GitHub Pages Site

One quick statement here that needs be addressed is, you dont have to use Chirpy but I highly recommend you stick with it for this build and then explore other themes once you have built this site out for yourself. Many refrences and videos online refer to being able to make a GitHub pages repo and use the theme builder button that use to be backed into the settings > pages > Build and Deployment section. This was depreciated in 2022 for security concerns. 

[https://github.com/orgs/community/discussions/32750](https://github.com/orgs/community/discussions/32750)

[https://github.blog/changelog/2022-08-22-github-pages-deprecating-the-theme-picker/](https://github.blog/changelog/2022-08-22-github-pages-deprecating-the-theme-picker/)

[https://github.com/orgs/community/discussions/33924](https://github.com/orgs/community/discussions/33924)

A list of Jekyll themes can be found here:

[https://jekyllrb.com/docs/themes/](https://jekyllrb.com/docs/themes/)

Additional Resources for Jekyll Themes and building Jekyll sites:
[https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)

[https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)

[https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)

|How To Build A Website | Github Pages | Jekyll | Template

[(547) How To Build A Website Github Pages  Jekyll Template - YouTube](https://www.youtube.com/watch?v=g6AJ9qPPoyc)

---

## Modifying \_config.yml

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

![Pasted image 20230507123105.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507123105.png)

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

![Pasted image 20230507123639.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507123639.png)

![Pasted image 20230507123706.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507123706.png)

yes

![Pasted image 20230507123713.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507123713.png)


Then sync


![Pasted image 20230507123751.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507123751.png)

Yes, push & pull

Open your remote images GitHub repo in your web browser and find the file. 

![Pasted image 20230507124002.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507124002.png)

Click into the your image file and the right click on the image and open in new tab.

![Pasted image 20230507124121.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507124121.png)

This is the cdn content path for your stored images on GitHub CDN.

https://raw.githubusercontent.com/Your_GitHub_Path

You will then copy that path and use that path as your avatar url path. 

This method will also be used as an alternative to storing screenshots into GitHub CDN so we can embed images into or blog posts. 

if your intrested in how this works check out this video on Cross-Origin Resource Sharing (CORS). 

[CORS in 100 Seconds - YouTube](https://www.youtube.com/watch?v=4KHiSt0oLJ0)
- CORS in 100 Seconds
  
Also check out emeding images into markdown as I will briefly go over this again later. 

[GitHub Pages: Images with Markdown - YouTube](https://www.youtube.com/watch?v=_1vmtqnKQew)
- GitHub Pages: Images with Markdown

Better video by same Web Craftie Creator.

[GitHub Pages: Image as Links - YouTube](https://www.youtube.com/watch?v=iDUuTmnH-0A)
- GitHub Pages: Image as Links
  
Now that you have setup your config file you can check your local site and see if its updated those changes. If it has not then you may need to ctrl+c, Y, Y and just redeploy the site once more.  

You are now ready to start creating content. 

## Creating Posts via Markdown files

Your website is all stood up and somewhat configured and now your eager to start posting. The next section is about markdown and posting content. 

Make sure you have preinstalled Obsidian and created a vault within the same directory as our github.io local workspace where our jekyll site is being written. 

Open the vault in the root directory of the GitHub pages repo. 

Add the .obsidian path to the .gitignore file.

![Pasted image 20230507131405.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507131405.png)

Okey, Obsidian will come into play later at the end of this section for know focus on just building our first post using VSCode. 

Navigate to \_posts 

Right click and add file to this folder. 

Title the file: 
	YYYY-MM-DD-post-info.md

Like below

	2023-05-07-My-First-Post.md

If you dont use this method your posts wont show up bc chirpy looks for the date prepended unlike some online tutorials I found. This was a real pain in the ass bc I thought it was my build that jacked up but no really it was syntax match for the date. 

[https://stackoverflow.com/questions/30625044/jekyll-post-not-generated](https://stackoverflow.com/questions/30625044/jekyll-post-not-generated)

The first thing Jekyll and Chirpy are going to look for is the Front Matter at the beginning of the post. See Writing a New Post by chirpy for more info:

[https://chirpy.cotes.page/posts/write-a-new-post/](https://chirpy.cotes.page/posts/write-a-new-post/)

The Front Matter you will need on every post:

[https://jekyllrb.com/docs/front-matter/](https://jekyllrb.com/docs/front-matter/)

```YAML
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-TTTT
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG]     # TAG names should always be lowercase
---
```

My Front Matter looks like this and I use this as my template in Obsidian which I will go into in a moment. 

```YAML
---
title: 
date: 2023-05-06 12:00:00 -500
categories: [,] # Can only be two categories
tags: [,] # Must not have any open commas and use lowercase
---
```

I have this saved in Obsidian using Templater by SilentVoid

![Pasted image 20230507132849.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507132849.png)

Check out this article by TrustedSec which dropped me in on the use of Obsidians Templater Plugin:

[https://www.trustedsec.com/blog/obsidian-taming-a-collective-consciousness/](https://www.trustedsec.com/blog/obsidian-taming-a-collective-consciousness/)

For now we wont worry about setting that up. Just add your title your date and timezone select categories. Example:

```YAML
---
title: My First Post 
date: 2023-05-06 12:00:00 -500
categories: [Posts,GitHub.io]
tags: [Jekyll,Website,GitHub,Code]
---
```

Now your ready to start exploring the markdown language. 

There are many guides on the internet for markdown. I recommend getting a quick rundown from the Techno Tim Video I posted above.  Ctrl + F > Techno Tim

Slap together some headers using # and add some code by using tab or \`\`\` Python
make sure to close it with \`\`\` and also add the code.

You can also search my posts on this website for more tips and tricks about markdown.

Now that you have written your post you need to save it in the VScode. 

You should now be able to refresh and see your post on the local instance of VSCode.

Know all we need to do is push commit our changes locally and push the code to the main repo and GitHub actions will be run on GitHub pages repo to Build and Deploy our site. This is bc the Chirpy template has a workflow prebuilt in the file .github/workflows/pages-deploy.yml 

This deploy yml basically just specifies GitHub actions configs. I went down a rabbit hole hear but this should all be setup and not necessary to modify anything for our site to work. If your intrested in GitHub actions check out some resources I found that really helped me understand what was going on when we pushed our code to the remote repo for GitHub actions to auto Build and Deploy. 

[GitHub Action Workflows on Mac, Windows and Linux - YouTube](https://www.youtube.com/watch?v=oW5MOjv6kBo)
- GitHub Action Workflows on Mac, Windows and Linux
  
Troubleshooting actions or checking actions status can be found under the Actions tab in your GitHub pages repo. 

![Pasted image 20230507134450.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507134450.png)

Once the Repo Builds and Deploys navigate to your site URL and take a look. You have just created a website that you can now upload posts about yourself and the work you do. This is a fundemental step in giving back to your community. 

Its also a good place to showcase your skills and even place your CV/Resume here as well. More on this to come. 

[https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/setting-a-markdown-processor-for-your-github-pages-site-using-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/setting-a-markdown-processor-for-your-github-pages-site-using-jekyll)


---

## Obsidian Enhancements

There are three main bonuses to using Obsidian and VSCode in tamdum to create your content. 

1. Obsidian is way to good at helping you create markdown
2. Obsidian is way to good at templating and commiting code GitHub using plugins
3. Obsidian is a killer notetaking app in general

Here is a rundown of how to quickly get up and running with Obsidian.

As stated before you should have created a vault in the root directory of your GitHub pages repo locally. 

Before we use the tool we need to make some config changes and also add some plugins. 

Navigate to Settings (The cog wheel on the bottom left.) > then navigate to File and Links > then scroll down and add a defualt location for attachements. Change the dropdown to, "In the folder specified below." > Type folder path, "images", for the folder you created to store files so github cdn can be used. 

![Pasted image 20230507135348.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507135348.png)

Now navigate to Community Plugins and Enable them and then click browse.

![Pasted image 20230507135607.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507135607.png)

now browse to, Obsidian Git by Vinzent

![Pasted image 20230507135756.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507135756.png)

Select it. 

Now browse to templater by SilentVoid 

![Pasted image 20230507135811.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507135811.png)

Now enable those Plugins here.

![Pasted image 20230507135841.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507135841.png)


Okey, one last step. Configuring and setting up the plugins.

To setup Obsidian Git all you need to do is create shortcuts for Commit, Push and Pull. This is becuase we already setup GIT, and did our initial commits via VSCode. So obsidian detects the .git and .gitignor files that were created when we cloned the repo down to our local machine.

See my blog post about, [ https://xp101t7.github.io/posts/Init-Git-Repo-Obsidian/](https://xp101t7.github.io/posts/Init-Git-Repo-Obsidian/)
For more info on, "# Initialiaze Git Repo and Set Obsidian Git Auto Backup on already".

Go into Settings> Hotkeys > and add shorcuts as follows

![Pasted image 20230507140507.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507140507.png)

![Pasted image 20230507140500.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507140500.png)

![Pasted image 20230507140503.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507140503.png)

Now test Commit first, then do a Push. Pull will come in handy when you need to pull changes from your remote repo that have not been synced yet downstream. 

Obsidian Git is all set if you have any issues see my other posts about GitHub and Obsidian.

One last change needs to be done with Templetar configs. 

Go to settings > community plugins > Templater 

![Pasted image 20230507140754.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507140754.png)

Create a templates folder using mkdir or by adding a new folder to the root directory using vscode or obsidian. 

now add that templates folder to the Templater config. 

![Pasted image 20230507140914.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507140914.png)

Now any files you create under this folder can be accessed as templates. I just templated the Front Matter so i could quickly add a new post template under \_posts. 

You can access the templates using command pallete or by pressing, ALT+N to open the list of Templates located under the Template folder location. 

When you select a template it will appear at the bottom of obsidian as Untitled.

![Pasted image 20230507141203.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507141203.png)

You just need to drag that into the \_posts folder and then your ready to use Obsidians power auto markdown creation to write blog posts and then commit them to GitHub with the shortcuts we set above. #Note Remember that you first need to commit before you push or pull. 

---

## Images Embedding

We discussed this ealier but this is one way to how we will add images using GitHub CDN and then use VScode to auto update the links.

First thing to understand is that you can just screen clip and drop things into Obsidian now inside this vault and it will autostore the files in the images folder. 

If you are using images that are not auto added to this folder you need to place them in there. 

Once your images in obsidian are linked you can then commit and push these images to the remote repo. These images will then be out on the internet in the public repo under the images folder and also stored under:

https://raw.githubusercontent.com/

So now that we have stored the images in that repo we can navigate to our site and see that this links are not updating as they should be bc the way Jekyll Builds and embeds the refrences to these links in the site. 

So if we navigate to one of the images and do the right click we can see they are sitting at the raw gitusercontent site. The logic here is to use the location they are stored to map the links to those files on the CDN endpoints to our markdown code. 

We can do this by taking the path to you remote folder where those images are stored and then mass changing the markdown links to point to your folderpath remotely where the images are stored. 

https://raw.githubusercontent.com/yourgithubusername/yoursite.github.io/main/images/

We can do this after we have commited the files using VSCode and some multi line cursor shortcuts and another plugin called, "Update relative links".

![Pasted image 20230507142426.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507142426.png)


After adding the plugin you need to make a quick modification to the configs 



![Pasted image 20230507143034.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507143034.png)

The links should work well with Relative links updater with shortes path when possible.

![Pasted image 20230507143039.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507143039.png)


So add this plugin and then open command pallete, ctrl+p > Type Update and click 
![Pasted image 20230507142618.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507142618.png)
![Pasted image 20230507142623.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507142623.png)

Now push these changes to the remote repository using the Git Obsidian shortcuts. #Note this could have been done proir to the push of the images as well. Also the changing of the files path to the remote repo can be done before pushing the files as well but they will not CORS without them being pushed remote first.

So now that the links are updated we can mass change the embeded links in the posts markdown file in VScode quickly.

Find the first instance of an image link being refrenced. 


![Pasted image 20230507143256.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507143256.png)


Copy the first portion of the local shortest path refrence. https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images

![Pasted image 20230507143403.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507143403.png)

Then press ctrl+shift+l to highlight all the instances of that varaible in your code. 

Now navigate to your GitHub CDN stored file path and copy the all the way up to the images folder. 

https://raw.githubusercontent.com/yourgithubusername/yoursite.github.io/main/images

Now delete all the instances of local path and past in the CDN address to replace. Also remove the additional \[ bracket prepended to the start.

Now click anywhere to remove the multiline cursor and then commit and push using Obsidian Git or via VScode source control.

The final link should look like 

![Pasted image 20230507143955.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507143955.png)

If you commit your post before changing the relative links you will break the build process in GitHub actions bc the files cannot be found. You first need to mass update the pointers to the CDN of your choosing then commit and the GitHub actions will be able to find the Image files and the site will build. 

![Pasted image 20230507145503.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230507145503.png)


You are now all finished and can look in your remote repo to see your posts get rapily built out. 

[https://stackoverflow.com/questions/40197197/jekyll-how-to-display-an-image-in-a-post](https://stackoverflow.com/questions/40197197/jekyll-how-to-display-an-image-in-a-post)


---

## Adding Linked-in Links

Just uncomment the linked-in refrences in your config.yml file and \_data > contanct.yml and share.yml files.

Also add the code snippet for username same as github and twitter. This will help you use your site as for job interviews.

---

## Additional Value Adds Coming In Future Blog Posts

- Adding your resume to the Site. 
	- [Create a Free Website Using Jekyll and Host on Github - Very Fast and Secure DIY EASY - YouTube](https://www.youtube.com/watch?v=GEunDyG_C54)
	- [https://www.alteredtech.io/posts/resume/](https://www.alteredtech.io/posts/resume/)
	- [https://carolstran.github.io/cv/](https://carolstran.github.io/cv/)
- Adding a domain name to the site.
- Linking DynDNS or Static IP to the site.
- Setting up a better CDN for images.
- Creating pull requests to practice code colloboration.
- Adding live action GIFs to your markdown posts.
- Using Memex to enhance your posts.
- GitHub Actions rabbit holing.
- Adding comments to your posts.
- Much, Much, More...

---

## F***\*in Favicons

Everything is now complete except for those pisky favicons on the remote repo that need to be changed to reflect your creative image. 

Check out my post, "Favicon Changes for Jekyll Site", to find out how I changed the favicons for my site. 

[https://xp101t7.github.io/posts/Favicon-Changes/](https://xp101t7.github.io/posts/Favicon-Changes/)


---

## Links and Resources

* [cotes2020/jekyll-theme-chirpy: A minimal, responsive and feature-rich Jekyll theme for technical writing.](https://github.com/cotes2020/jekyll-theme-chirpy#documentation)
* [Getting Started Chirpy](https://chirpy.cotes.page/posts/getting-started/)
* [Jekyll on Windows Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/installation/windows/)
* [Installation Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/installation/)
* [Downloads](https://rubyinstaller.org/downloads/)
* [cotes2020/chirpy-starter: The startup template for the Chirpy.](https://github.com/cotes2020/chirpy-starter#installation)
* [Creating a GitHub Pages site with Jekyll - GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)
* [Github pages wizard, where is the theme chooser? · community · Discussion #32750](https://github.com/orgs/community/discussions/32750)
* [Jekyll post not generated - Stack Overflow](https://stackoverflow.com/questions/30625044/jekyll-post-not-generated)
* [Writing a New Post Chirpy](https://chirpy.cotes.page/posts/write-a-new-post/)
* [Front Matter Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/front-matter/)
* [(548) How To Build A Website  Github Pages Jekyll Template - YouTube](https://www.youtube.com/watch?v=g6AJ9qPPoyc)
* [Testing your GitHub Pages site locally with Jekyll - GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
* [Adding a theme to your GitHub Pages site using Jekyll - GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)
* [where has the theme-chooser gone !? is it still in use !? · community · Discussion #33924](https://github.com/orgs/community/discussions/33924)
* [GitHub Pages: Deprecating the theme picker GitHub Changelog](https://github.blog/changelog/2022-08-22-github-pages-deprecating-the-theme-picker/)
* [(548) GitHub Pages: Image as Links - YouTube](https://www.youtube.com/watch?v=iDUuTmnH-0A)
* [(548) GitHub Pages: Images with Markdown - YouTube](https://www.youtube.com/watch?v=_1vmtqnKQew)
* [(548) CORS in 100 Seconds - YouTube](https://www.youtube.com/watch?v=4KHiSt0oLJ0)
* [(548) Meet Jekyll - The Static Site Generator - YouTube](https://www.youtube.com/watch?v=F8iOU1ci19Q&t=248s)
* [Stock photos, royalty-free images, graphics, vectors & videos Adobe Stock](https://stock.adobe.com/)
* [(548) GitHub Action Workflows on Mac, Windows and Linux - YouTube](https://www.youtube.com/watch?v=oW5MOjv6kBo)
* [Obsidian, Taming a Collective Consciousness - TrustedSec](https://www.trustedsec.com/blog/obsidian-taming-a-collective-consciousness/)
* [(548) Create a Free Website Using Jekyll and Host on Github - Very Fast and Secure DIY EASY - YouTube](https://www.youtube.com/watch?v=GEunDyG_C54)
* [(548) Go Go gh-pages! Build a website with GitHub Pages and Jekyll (workshop) - YouTube](https://www.youtube.com/watch?v=SWVjQsvQocA)
* [Favicon Generator for perfect icons on all browsers](https://realfavicongenerator.net/)
* [Creating a Jekyll resume with a theme AlteredTech](https://www.alteredtech.io/posts/resume/)
* [(548) Getting Started with GitHub Pages - YouTube](https://www.youtube.com/watch?v=QyFcl_Fba-k)
* [Jekyll how to display an image in a post - Stack Overflow](https://stackoverflow.com/questions/40197197/jekyll-how-to-display-an-image-in-a-post)
* [Carolyn Stransky  💻✒️📄🌟](https://carolstran.github.io/cv/)
* [Setting a Markdown processor for your GitHub Pages site using Jekyll - GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/setting-a-markdown-processor-for-your-github-pages-site-using-jekyll)
* [Troubleshooting Jekyll build errors for GitHub Pages sites - GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/troubleshooting-jekyll-build-errors-for-github-pages-sites)

















