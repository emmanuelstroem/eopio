---
date: 2018-06-08
title: "macOS Mojave: Guides"
description: |
  
tags: ["macOS", "mojave", "guides"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

## Upgrading to macOS Mojave (Beta)
#### 1.  Pre-Requisites:
- Free/Paid Apple Developer account

#### 2. Steps:
- Download and install configuration file from Developer Portal:
    [https://download.developer.apple.com/WWDC_2018/macOS_10.14_Developer_Beta_Access_Utility/macOSDeveloperBetaAccessUtility.dmg](https://download.developer.apple.com/WWDC_2018/macOS_10.14_Developer_Beta_Access_Utility/macOSDeveloperBetaAccessUtility.dmg)
    
- Requires a Restart
- Find macOS Mojave update in the Apple AppStore on the Mac.
- Follow Instructions to Install.


## Making Bootable USB for macOS Mojave
#### 1. Pre-requisites
- Installer for macOS Mojave. Can be downloaded from Apple portal after login into the Developer Portal (steps same as when upgrading).
- USB stick/flash at lease 8GB

#### 2. Steps
- Format USB drive to Mac OS Extended (Journaled) FileSystem using Disk Utility
- Give it a name e.g UNTITLED
- Open ```Terminal```
-  Run the command:
-  ```sudo /Applications/Install\ macOS\ 10.14\ Beta.app/Contents/Resources/createinstallmedia --volume /Volumes/UNTITLED --nointeraction```

When asked for password, enter your admin user password

Note: 
```-- nointeraction``` lets the terminal execute the command with (yes) to all the yes/no prompts.

End Result:
￼```Ready to start.
To continue we need to erase the volume at /Volumes/UNTITLED.
If you wish to continue type (Y) then press return: Y
Erasing disk: 0%... 10%... 20%... 30%... 100%
Copying to disk: 0%... 10%... 20%... 30%... 40%... 50%... 60%... 70%... 80%... 90%... 100%
Making disk bootable...
Copying boot files...
Install media now available at "/Volumes/Install macOS 10.14 Beta"```

## Setting up Dev Env in macOS Mojave

### 1. Pre-Requisistes:
- Xcode 10.0 Beta: 
    - [Download Xcode 10 Beta](https://download.developer.apple.com/Developer_Tools/Xcode_10_Beta/Xcode_10_Beta.xip)
- Ensure that Xcode 10 beta is active using the command:
    - ```sudo xcode-select --switch /Applications/Xcode-beta.app ```


### 2. Steps
- Install [homebrew](https://brew.sh) using:
     - ```/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"```
- Install pkg-config
    - ```brew install pkg-config```
- IMPORTANT: Change permissions og pkg-config using:
    - ```chmod 755 /usr/local/Cellar/pkg-config/0.29.2/bin/pkg-config```
- Install rbenv using:
    - ```brew install rbenv```

And install any other development environment packages you require.

Don't hesitate to reach out to me for any inputs/suggestion/assistance.

Best of Luck.
