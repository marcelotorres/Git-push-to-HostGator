## Overview

FTP blows and you want to simply `git push hostgator master` out to your desired directory. It's pretty easy.

## Assumptions

* You've gotten Hostgator to authorize you to use SSH
* The server has git
* You know that Hostgator uses port 2222 for SSH

## Step 1: Put your public key on the server

Go ahead and SSH into the box:

``` 
ssh USER_NAME@your-domain.com -p 2222
...
Enter your password: *****
```

Copy your public key from your machine. It's typically found in `~/.ssh/id_rsa.pub`. Open that file in Sublime (or something) and copy it. Then, on the Hostgator server, paste it in the following file: `~/.ssh/authorized_keys`. It's possible that the **authorize_keys** file may not exist. If that's the case just `touch authorized_keys` in that directory to create it.

## Step 2: Initialize a repo and configure it

`cd` into your directory. An example might be `/www/your-domain.com`. Initialize the repo with:

```
git init
```

After that, configure the repo to accept pushes into this working directory.  Run this:

```
git config --add receive.denyCurrentBranch ignore
```
Of course, once commits are pushed, they must be checked out. Save the following in a file at: `PATH_TO_REPO/.git/hooks/post-receive`

```
GIT_WORK_TREE=../ git checkout -f
```

Then make it executable with:

```
chmod +x PATH_TO_REPO/.git/hooks/post-receive
```

## Step 3: Setup your SSH config locally

Let's make it simple to connect to hostgator via SSH. Open `~/.ssh/config` on your local machine and add an entry to it.

```
Host hostgator
    User USER_NAME
    HostName YOUR_DOMAIN
    Port 2222
    PreferredAuthentications publickey
```

Now, you can simply `ssh hostgator` and get a connection.

## Step 4: Setup Hostgator as a git remote

On your local machine, navigate to your repo and run the following command:

```
git remote add hostgator ssh://user-name@your-domain.com:2222/full/path/public_html/your-domain.com
```

I had to add the full path to get everything to work properly. You can find the full path in your control panel.

## Step 5: git push

Now you can simply:

```
git push hostgator master
```

Done.
