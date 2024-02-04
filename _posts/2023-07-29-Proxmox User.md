---
title: Add a New Proxmox User from the Terminal
date: 2023-07-29 12:00:00 -500
categories: [proxmox,homelab]
tags: [lab,Terminal,pveum,Proxmox]

---

> The thing that is disliked by me is also disliked by others. Since I dislike this thing, how can I inflict it on someone else?
> — <cite>The Buddha</cite>

---

## Add a New Proxmox User from the Terminal

In Proxmox, you can create a new user account from the command line. Here's a step-by-step guide to creating a new user:

1. **Log in to the Proxmox server** as the root user or another user with administrative privileges.
    
2. **Use the `pveum` command** to create a new user. Here's an example command that creates a new user with the username `newuser` in the `pve` realm:
    
    bashCopy code
    
    `pveum useradd newuser@pve -password 'YourPasswordHere' -firstname 'First' -lastname 'Last'`
    
    Replace `'YourPasswordHere'` with the desired password, and `'First'` and `'Last'` with the user's first and last names.
    
3. **Assign permissions** to the new user as needed. For example, to give the new user administrative privileges, you can use the following command:
    
    bashCopy code
    
    `pveum aclmod / -user newuser@pve -role Administrator`
    
    You can replace `Administrator` with another role if you want to assign different permissions.
    
4. **Verify the new user**: You can list all users to verify that the new user has been created:
    
    bashCopy code
    
    `pveum user list`
    
5. **Log in with the new user**: You should now be able to log in to the Proxmox web interface using the new user's credentials.
    

Please note that the exact commands and options might vary depending on your specific Proxmox version and configuration. Always refer to the official Proxmox documentation or consult with an IT professional if you're unsure.

Also, be mindful of security best practices when handling usernames and passwords, especially if you are working on a production system or dealing with sensitive data.


 