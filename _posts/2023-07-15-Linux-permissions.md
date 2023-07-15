---
title: Sysadmin - Linux File Permissions 
date: 2023-07-15 12:00:00 -500
categories: [Permissions,Linux]
tags: [Terminal,ACL,Permissions,Linux]


---

> Computers are useless. They can only give you answers.
> — <cite>Pablo Picasso</cite>

---

![Pasted image 20230715104758.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/Media/Pasted%20image%2020230715104758.png)

### Linux File Permissions


To check the permissions of files on Linux, you can use the `ls` command with the `-l` option. Here's how you can do it:
1. Open the terminal.
2. Navigate to the directory where the file is located using the `cd` command. For example, if the file is in the home directory, you can use `cd ~` to navigate to it.
3. Once you are in the correct directory, use the `ls -l` command to list the files and their permissions. This will display a detailed list of files, including their permissions, ownership, size, and other information.

The output of the `ls -l` command will look something like this:

```
-rw-r--r-- 1 user group 1024 Jan 1 10:00 myfile.txt
drwxr-xr-x 2 user group 4096 Jan 1 10:00 mydirectory
```

In the above example, the first column represents the file type and permissions. The permissions are represented by a combination of letters and symbols. The first character indicates the file type, where `-` represents a regular file and `d` represents a directory.

In the output of the `ls -l` command, the next three characters after the file type represent the owner's permissions. The three characters represent the read (`r`), write (`w`), and execute (`x`) permissions for the owner of the file.

For example, in the output `-rw-r--r-- 1 user group 1024 Jan 1 10:00 myfile.txt`, the owner has read and write permissions (`rw-`).

The next three characters represent the group's permissions. These permissions indicate the read, write, and execute permissions for the group that the file belongs to.

Continuing with the example, in the output `-rw-r--r-- 1 user group 1024 Jan 1 10:00 myfile.txt`, the group has read-only permissions (`r--`).

Finally, the last three characters represent the permissions for other users or the world. These permissions indicate the read, write, and execute permissions for all other users who are not the owner or part of the group.

In the example `-rw-r--r-- 1 user group 1024 Jan 1 10:00 myfile.txt`, other users have read-only permissions (`r--`). This means that users who are not the owner or part of the group can only read the file but cannot modify or execute it.

The number `1` after the permissions indicates the number of hard links to the file. Hard links are additional names for the same file. In this case, there is only one hard link to the file.

The `user` and `group` in the output represent the owner and group of the file, respectively. In the example, the file is owned by the user named "user" and belongs to the group named "group".

The `1024` in the output represents the file size in bytes. In this case, the file size is 1024 bytes.

The remaining information in the output includes the date and time of the last modification of the file (`Jan 1 10:00`) and the file name (`myfile.txt`).

By using the `ls -l` command, you can easily view the permissions of files in a directory and understand the level of access granted to different users and groups.`


I  SSH to app 2 then chmod +x on the .sh file and the execute failed but It had x priv by all users. 

The lab is in review. 

---

