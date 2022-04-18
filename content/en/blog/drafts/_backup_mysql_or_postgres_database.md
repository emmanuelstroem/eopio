---
date: YYYY-mm-dd
title: "Draft Post Template"
description: |
  
tags: [""]
draft: true
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

Cronjob to run daily at 1am

```
0 1 * * * mysqldump db_name | gzip > /path_to_backups/mysql/db_name_$(date +%d_%m_%Y).sql.gz
```

Detailed explanation on in this post: 

Above works if `.my.cnf` file exits under current linux user with correct mysql user credentials.
Otherwise, you need to provide inline user and password. e.g:
```
mysqldump -u db_user -p db_pass --databases db_name | gzip > /path_to_backups/mysql/db_name_$(date +%d_%m_%Y).sql.gz
```

`$(date +%d_%m_%Y)` - just appends date stamp to the backup name. 

`$(date +”%d_%m_%Y_%I_%M”)` - would append date & time stamp incase of more regular backups.

`gzip` pipe command just compresses the dump to approximately `70%` of its original size to save disk space
