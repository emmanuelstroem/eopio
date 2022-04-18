---
date: 2017-11-30
title: "macOS root user security issue and how to fix it"
description: |
  
tags: ["macOS", "security", "root"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

## What is *root* User?
**Root**  user is a special user account used for System Administration in Unix and Linux distros.
The name of the account (root) is not the determining factor always. In Unix for example is UID. any account with UID = 0 is the superuser (root) account.
In other systems, its role-based.

## About this Security issue
In macOS, the Security flaw is that, this **root** user account does not require a password/verification/authenticaion to grant access to its privileges for any action on the system.

Possible actions include:
    - Logging into the Mac System
    - Changing Permission of files
    - Changing System Preferences
    - Creating and Deleting user accounts . . .
List goes on.

I have recorded a short screencast to show you the live scenario.

<iframe width="560" height="315" src="https://www.youtube.com/embed/2sWUGnzTIZQ?rel=0" frameborder="0" allowfullscreen></iframe>



## Solution

### 1. Official Fix From Apple (Easy Way)
Apple on 29th Nov 2017, finally released an official fix to the root user bug in macOS High Sierra 10.13.x.
More details here: https://support.apple.com/en-my/HT208315

![security-update--for-root-user-fix-on-macos-high-sierra-app-store](http://eopio.com/content/images/2017/12/security-update--for-root-user-fix-on-macos-high-sierra-app-store.png)

Ensure to install this security update from the App Store on your Mac ASAP.

If you have Auto Install Security Updates enabled on your Mac, your system will install this and all other security updates automatically.
You'll see a notification like this once install of this security update is complete.

![security-update-installed-macos-high-sierra-root-user](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/osx/security_update_installed.png)

To Turn ON Atutomatic Security Updates (highly recommended), please use the instructions here: https://support.apple.com/en-us/HT204536


### 2. Additional Ways to Fix root user access (Hard Way)

Apple on 28th Nov 2017, posted an article to help users either *disable* or set **root** password: https://support.apple.com/en-my/HT204012

Here is what the article entails.

#### How to Enable or Disable root user:
1. Choose Apple menu () > System Preferences, then click Users & Groups (or Accounts).
2. Click lock icon, then enter an administrator name and password.
3. Click Login Options.
4. Click Join (or Edit).
5. Click Open Directory Utility.
6. Click lock icon in the Directory Utility window, then enter an administrator name and password.
7. From the menu bar in Directory Utility:
        - Choose Edit > Enable Root User, then enter the password that you want to use for the root user.
        - Or choose Edit > Disable Root User.
    

#### How to Log in as the root user
When the root user is enabled (which it is by default), you have the privileges of the root user only while logged in as the root user.

1. Choose Apple menu > Log Out to log out of your current user account.
2. At the login window, log in with the user name ”root” and the password you created for the root user.

If the login window is a list of users, click Other, then log in.
Remember to disable the root user after completing your task. 


#### How to Change the root password
1. Choose Apple menu () > System Preferences, then click Users & Groups (or Accounts).
2. Click lock icon, then enter an administrator name and password.
3. Click Login Options.
4. Click Join (or Edit).
5. Click Open Directory Utility.
6. Click lock icon in the Directory Utility window, then enter an administrator name and password.
7. From the menu bar in Directory Utility, choose Edit > Change Root Password…
8. Enter a root password when prompted.


Please take the time out to make the necessary changes to your system since this flaw puts your Mac system at risk whenever its out of your sight.

Good Luck!
