---
title: Remote Hosted Cybersecurity GPT ChatBOT Named "MyCybersecHelper" 
date: 2023-05-06 12:00:00 -500
categories: [github,vscode]
tags: [coding,vscode,git,github]
---

# 
# Remote Hosted Cybersecurity GPT ChatBOT Named "MyCybersecHelper"

This markdown guide goes into deep detail on how to Code a GPT Chatbot using your local VSCode as a test bed AWS Ubuntu VM, VScode, Openai (GPT 3.5), gradio ML python module  leveraging gradio app tunneled through hugginface spaces repository. (Persistent sharable Gradio links is still a work in progress.)

This guide also assists users with: 
- SSH to EC2 Instance
- RDP to Remote Instance
- Installing GUI instances from Ubuntu Server mode
- Install VS Code
- Change python code to use secure code by setting environment variables 
- Setup .env files 
- Setup .gitignore files 
- Install python 
- Setup VS code for python dev environment 
- Clone respositories to your local host
- Setup Git 
- Commit Git Files
- Pull code changes to remote repositories
- Setting up Hugginface spaces
- Learning GPT API
- Learning to Customize GPT Chatbot assigning custom GPT language models (Act like Cybersecurity Engineer when answering questions.)
- Setup a local instance of the Chatbot and not share it out using HuggingFaces.
- And more...

#### Current working link: https://0dee44f4271f2b5664.gradio.live

GPT = Generative Pre-Trained Transformer 

"Allows for a small amount of input text to generate large volumns and relevant and sophisticated machine generated content" - Dr. Alex Young - [Is This The FUTURE Of Note-Taking? (ChatGPT For Studying) - YouTube](https://www.youtube.com/watch?v=YN1yeis37G0)

### NOTE: If you havn't installed VScode on your local machine follow the guide below as you will need VS Code fully setup with python to start this guide.
[How to setup Python for VSCode in 2023 in 5mins! | Install Python and Setup VSCode for Windows 10 - YouTube](https://www.youtube.com/watch?v=cUAK4x_7thA)

## Start by coding the Chatbot Locally 

Once VS Code is installed and python dev environment is setup then continue 
[ChatGPT in Python for Beginners - Build A Chatbot - YouTube](https://www.youtube.com/watch?v=pGOyw_M1mNE)
-   <a class="youtubeTimestamp" href="https://www.youtube.com/watch?v=pGOyw_M1mNE&t=623">10:23</a>

This code will need to be modified, you dont want to store you api_keys in your code in clear text. 

https://github.com/AIAdvantage/chatgpt-api-youtube
https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety

Get the API keys from Chat GPT you will have to signup for a paid account. The request volume should be low and each API req is like .002 Cents per 1k tokens.

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416114944.png)

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416114831.png)

For GPT 4 Pricing

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416114951.png)

We are using GPT-3.5-Turbo

### Set Environment Variables

Set your api_key as environment variables either using local instance export commands that do not persist the current terminal session or using .env and .gitignore files.

```bash
echo gpt_api_key
export gpt_api_key='sk-<apikey>'
echo $gpt_api_key
```

Replace the code in the youtube video with the below python code which uses chat gpt function for grabbing the env. var. using the OS module.

```Python
import os

openai.api_key =  os.environ["gpt_api_key"]
```

Test the Bot locallyce

Again, another method of storing the variable is by using .env file that you need to remember to .gitignore. The code for that modification is as follows. https://blog.gitguardian.com/how-to-handle-secrets-in-python/
https://www.toptal.com/developers/gitignore/

```Python
from dotenv import dotenv_values
import os

secrets=dotenv_values(".env")

#using the secrets
def main():
	print(secrets["gpt_api_key"])
    
if __name__ == "__main__":
	main()
```

or another way that I couldn't quite get working 
https://www.python-engineer.com/posts/dotenv-python/

```Python
import os
from dotenv import load_dotenv

load_dotenv()

gpt_api_key = os.getenv("gpt_api_key")

openai.api_key = "gpt_api_key"
```

once testing is complete locally we now want to spin up a cloud server where you can deploy this code and other bits of code on a resource that is not attached to your local network. Whoever it's still ideal If you have a lab environment to set it up on a stand alone server or docker container to replicate.

# Setting up EC2 Instance for Web Hosting MyCyberHelper Chatbot

First we want to install AWS EC2 instance of your choice. I chose Ubuntu and had struggles in the beggining with spinning up the Ubuntu-desktop gui using the free tier. I decided to spin up a bit beefier server using the below article as pricing guide.
https://dev.to/cindyledev/remote-development-with-visual-studio-code-on-aws-ec2-4cla

Follow this guide for spinning up the EC2 instance but change the server Instance type as you wish. Although there is a way to spin up the ubuntu server without a gui to get this running I did not use that method. I will be working and adding that into this guide at a later date. #FutureProject

Reference to spinning up the free tier server method: 
https://medium.com/nerd-for-tech/how-to-create-a-ubuntu-20-04-server-on-aws-ec2-elastic-cloud-computing-5b423b5bf635


#### SSH into the EC2 Instance

Once Instance is running connect to the instance over SSH, I had windows terminal issues and had to trouble shoot using the following guides. 
https://stackoverflow.com/questions/57363597/how-to-use-a-new-windows-terminal-app-for-ssh
https://stackoverflow.com/questions/24158177/ssh-is-not-recognized-as-an-internal-or-external-command

I just uninstalled and reinstalled the OpenSSH "Client" package and then restarted my terminal and SSH was working perfectly. I could have modified the settings.json and set the path to OpenSSH.  Also you can uninstall and resinstall using
https://stackoverflow.com/questions/24158177/ssh-is-not-recognized-as-an-internal-or-external-command#:~:text=Start%20%3E%20type%20%27Manage%20Optional%20Features%27

You can also use PuTTY as well just remember to select the .pek instead .pem private key in AWS secret keys creation menu. 

The AWS ssh connection command can be found under the, "connect" tab in the instance summary for your chatbot server

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416123226.png)

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416123246.png)

	ssh -i "~/.ssh/authorized_keys/your_private_key.pem" ubuntu@aws_server_dns_srv_addr.amazonaws.com

### Configure the Ubuntu Machine for RDP, Setup GUI, Change Password, Modify Ubuntu System for RDP connection Speed issue on free tier instances, Install VS Code, Update && Upgrade and run.

Now your connected to your server we need to run the following commands

```bash
sudo apt update && sudo apt upgrade
sudo apt install xrdp
sudo systemctl status xrdp
sudo passwd ubuntu
sudo apt install ubuntu-desktop
sudo adduser xrdp ssl-cert

//rdp speed up tricks settings for ubuntu
gsettings set org.gnome.desktop.interface enable-animations false
sudo sed -i 's/max_bpp=32/max_bpp=16/g' /etc/xrdp/xrdp.ini && sudo reboot

//Install VSCode
sudo apt install software-properties-common apt-transport-https wget
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt install code -y
code --version

//Update VSCODE and run
sudo apt update && sudo apt upgrade -y
code
```


### Setup Dev Environment in VS Code for Python, Git, OpenAI, Gradio,GitHub and Huggingface Spaces

Once you up and running and code is started we need to setup our dev environment

Go to the extensions tab in VS Code and install 
- Python
- Python extensions pack 
- Git-lens 
- GitHub Co-pilot 

Some of these extensions are for future use.

Now we need to install python using apt repo
	sudo apt install python3

#### Note: You can use any version of python but you will need to custom install one way is to go the Stable Releases Channel and sellect your Python Version of choice to run in a venv env. We wont be going into coding best practices and using a venv env in this demo but maybe a revision will come out in 2023. 

https://www.python.org/downloads/source/

Go to the Terminal and select "New Terminal" ctrl+shift+\`

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416123758.png)

From the command line install the pip modules for openai and gradio

	pip install openai
	pip install gradio

Now your ready to open your first project. I suggest cloning down the code from your GitHub repository that you forked off the intial local testing AIAdvantage repo from beginning of this lab. So basically you just commit your forked code changes up to your personal repo remotely. Then you can clone that remote repo back down to your AWS Ubuntu server instance. 

In your linux terminal open a new visual code window, then clone the remote repo for chatgpt-api-youtube or clone your code changes of the chatbot code you pushed to your forked remote repo I explained about above. (You dont have to fork...)

[How To Clone Git Project (The right way) - YouTube](https://www.youtube.com/watch?v=OFhO1s9WAk4)

	 code

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416125407.png)

https://github.com/AIAdvantage/chatgpt-api-youtube

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416125454.png)

Copy and paste the url into the clone git repo command pallete if you used the AIAdvantage repo then you will need to copy and paste your code from your machine over the top of his for the chatgpt chat assitant website.py file.

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416130256.png)

#### Set Local Environment Variables for the Remote Machine

Now you can set your local variables using a .env and .gitignore files or by setting it for that particular instance via the VSCode Terminal.

```Bash
//Set key to environment variables
echo gpt_api_key
export gpt_api_key='sk-<apikey>'
echo $gpt_api_key
```

Make sure you modified your code to import os and use os.environ['Your_key']

You can run the app from here but this is where you will get locked out from link sharing after 72 hours. Meaning the site goes down after 72 hours so you will need to write automation to run a cron job get the output of the running on public URL cmdline output and email to any users/friends that are intrested in your chatbots you stand up. OR figure out how to make the session persist using huggingface spaces.

Running on public URL: 
"This share link expires in 72 hours. For free permanent hosting and GPU upgrades (NEW!), check out Spaces: https://huggingface.co/spaces"

Currently I have created an account on Huggingface.co and linked my local repo to the code space there. You should do so yourself... as described below.

### Creating, Cloning HuggingFace Spaces Repo

Open a new VS Code window and and clone your newly created huggingface repo to your local machine.  It will prompt you for user name and login.

Now transfer your code from your local github workspace or the local AIAdvantage modified code workspace, whatever you decided to do, and paste it into a newly created python file under the huggingface workspace. You may need to transfer .gitignore and .env over as well. If you used local variables from export then run that command in the terminal again. 

Now you can commit the code changes back up to the newly created Hugging Face remote repo. 

#### Note: Its wise to use the same username for both huggingfaces and GitHub so when you commit git uses the same email and user name paramaters.

```Bash
git config --global user.email "your.email@gmail.com"
git config --global user.name "your-username"
```

### Commiting Code changes to the Remote HuggingFace Repo

Once that is set you can use source control to commit or use bash terminal 

![](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230416132505.png)

When you commit and sync your changes you will be prompted to enter a username and password for the remote repo in the command pallete at the top of VS Code window.

[How To Git Commit And Push Changes - YouTube](https://www.youtube.com/watch?v=9DHjfDuXMGA)

	git add --all
	git commit -m "Always be cracking!!!"
	git push

If you see an error look under the logging OUTPUT tab for the cuase of the issue. Make sure you set the email and username as I discribed above.

You are now all set to run your Code and then share out the link for the 72 hour limit.

## Get Gradio link to persist longer than 72 hours on huggingface spaces 

#FutureProject 
Im working on a solution for hosting gradio on huggingface spaces and that will be coming out soon. Written on 4/16/2023.

Showcase Your Projects in Spaces using Gradio
https://huggingface.co/blog/gradio-spaces

Sharing demos with others - Hugging Face Course
https://huggingface.co/course/chapter9/4?fw=pt

## Additonal Resources:

This didnt work well for me.

[How to Connect to Remote AWS EC2 Ubuntu GUI from Local Windows Machine - YouTube](https://www.youtube.com/watch?v=ndA4atuCqms)
-   <a class="youtubeTimestamp" href="https://www.youtube.com/watch?v=ndA4atuCqms&t=567">9:27</a>

https://cloudzy.com/knowledge-base/copy-paste-not-working-in-rdp/

[AWS EC2 SSH key management | How to launch and SSH into EC2 instance with public & private key pair - YouTube](https://www.youtube.com/watch?v=S5B09dq-jGQ)

Installing python ubuntu terminal

https://www.makeuseof.com/install-python-ubuntu/