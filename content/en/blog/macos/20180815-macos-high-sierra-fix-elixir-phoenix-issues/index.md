---
date: 2018-08-15
title: "macOS High Sierra: Fix Elixir+Phoenix Issues"
description: |
  
tags: ["macOS", "high sierra", "elixir phoenix"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

### Issue: ** (Mix) The task "phx.new" could not be found

### Elixir version

elixir `v1.7.2`


### Solution
Install latest phx using terminal command:

```mix archive.install https://raw.githubusercontent.com/phoenixframework/archives/master/phx_new.ez```

Run the command again and it should work.

If it doesn't feel free to contact me at any of the social channels to update this tutorial or with suggestions. :-)

Hope this was helpful!

