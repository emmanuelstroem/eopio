---
date: 2018-01-11
title: "macOS High Sierra: Setup Ruby on Rails"
description: |
  
tags: ["macOS", "high sierra", "ruby on rails"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

Pre-Requisites:
    * [Homebrew](https://brew.sh/)
    
Install rbenv using:
    ```brew install rbenv```

Add the following to your terminal profile (.bash_profile or .zshrc etc):
```
# rbenv
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

Relaunch the Terminal or enter the following comand for the profile changes to take effect
    ```source path_to_your_terminal_profile```

Ensure that Terminal launches with no errors.
If any, please check your syntax in terminal profile.

Verify that _rbenv_ is installed using:
    ```rbenv -v```
    
Install desired version of Ruby. As of this writing current stable version is 2.5.0
    ``` rbenv install 2.5.0```

Verify ruby versions installed using:
    ```rbenv versions```

Change permissions on rbenv ruby versions directories
    - ``` chmod ug+wx ~/.rbenv/versions```

The last command gives the _Users_ and _Groups_ Read and Write permissions to the _versions_ directory where all the gems shall be stored.
If any of the commands throw errors of PERMISSION, prepend ```sudo``` to the command and run it again.
e.g ``` sudo chmod ug+wx ~/.rbenv/versions```

Lastly, Install Rails:
    ```gem install rails``` or ``` sudo gem install rails ```

Confirm rails is installed using:
    ``` rails -v ```
    
And you're ready and set to start building Ruby on Rails applications.

Create your projects using:
    ``` rails new project_name```

This will create a folder _project_name_ and install all the necessary gems and dependencies.

Run your rails app:
    - ```cd project_name && rails server```

This spins up a server and runs your app on ```0.0.0.0:3000```
which is locally: ```localhost:3000```

More detailed Reads:
    - http://guides.rubyonrails.org/getting_started.html
    - https://www.ruby-lang.org/en/documentation/


Happy Railing!

Photo by [Matthew Mendez on Unsplash](https://unsplash.com/photos/zJAN2a0wHVk)
