---
title: Python Venv
date: 2023-09-22 12:00:00 -500
categories: [Venv,Python]
tags: [Bash,Coding,Venv,Python]

---

> We don't receive wisdom; we must discover it for ourselves after a journey that no one can take for us or spare us.
> — <cite>Marcel Proust</cite>

---

## Python Venv

### Step 1: Verify the Virtual Environment Directory

Ensure that the virtual environment has been created and that the `.venv` directory exists. Use the `ls` command to list the contents of the directory where you expect the virtual environment to be:


```
ls -a
```


If you don't see the `.venv` directory, it means that the virtual environment has not been created yet.

### Step 2: Create the Virtual Environment

If the virtual environment does not exist, create it using the following command:



```
python3 -m venv .venv
```


This command creates a virtual environment in the `.venv` directory.

### Step 3: Activate the Virtual Environment

After creating the virtual environment, try activating it using the source command:



```
source .venv/bin/activate
```


Now, you should be able to activate the virtual environment without seeing the "No such file or directory" error.

### Step 4: Verify the Activation

After activating the virtual environment, you can verify that it is activated by checking the command prompt; it should have `(.venv)` at the start.

### Note

- Ensure that you have the 

```
python3-venv
```

 package installed. You can install it using the following command on Debian/Ubuntu systems:
    


```
sudo apt-get install python3-venv
```

- It is good practice to work in a virtual environment when developing Python applications to manage dependencies more efficiently.

To deactivate a Python virtual environment (venv) that is currently active, you can use the `deactivate` command. Here is how you can do it:

### Step 1: Ensure You Are in the Virtual Environment

Before deactivating the virtual environment, ensure that you are currently in an active virtual environment. You can generally tell that a virtual environment is active because the environment's name will be displayed in your shell prompt, usually enclosed in parentheses, like this: `(venv)`.

### Step 2: Deactivate the Virtual Environment

To deactivate the virtual environment, simply run the following command:

```
deactivate
```


### Step 3: Verify the Virtual Environment is Deactivated

After running the `deactivate` command, the virtual environment should be deactivated, and you should return to the global environment. You can verify this by checking your shell prompt; the virtual environment name (`(venv)`) should no longer be displayed.

### Note

- The `deactivate` command is only available when a virtual environment is active. If no virtual environment is active, the `deactivate` command will not be found.
- After deactivating the virtual environment, you will be using the system-wide Python and packages again, and any packages installed in the virtual environment will not be available.

Try running the `deactivate` command to deactivate your virtual environment, and let me know if you have any questions or encounter any issues.
