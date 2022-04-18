---
date: YYYY-mm-dd
title: "Draft Post Template"
description: |
  
tags: [""]
draft: true
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

# Setup Server
- **Install unzip**
- install php
- install docker
- install nginx
- Install composer
- Install Laravel
- Install certbot


# Setup Deployer
- Install
    - composer require global deployer/deployer

- Edit deploy.php
    - Change //Hosts to the server config
    - Possible options are:

```
host('production')
    ->hostname('stackanswers.io')
    ->stage('production')
    ->user('manu')
    ->port(20022)
    ->identityFile('~/.ssh/id_rsa')
    ->forwardAgent(true)
    ->set('deploy_path', '{{webroot}}/{{application}}');
```

- Run Deploy:
    - ```dep deploy production```
