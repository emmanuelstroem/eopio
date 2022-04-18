---
date: 2019-04-02
title: "macOS: Setup New Elixir Project"
description: |
  
tags: ["macOS", "elixir", "project"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

Pre-Requisites:
- Brew
- Erlang
- Database Engine (CloudSQL, Postgres, MySQL, . . . )

Install Elixir:
```
brew install elixir
```

Create new project:
```
mix phx.new <project_name>
```

`phx.new` - pre-bakes Phoenix into the Elixir project. 
`mix new` - will only create an Elixir project without Phoenix.

This will create an elixir project in the current directory with the given name <project_name>
Fetch Dependencies:
```
cd <project_name>
mix deps.get
```

Configure Database (in config/dev.exs):	
- config/dev.exs for Development environment	
- config/prod.exs for Production environment

```
mix ecto.create
```

Start Phoenix app:
```
mix phx.server
```

Running your app inside IEx (Interactive Elixir):
```
iex -S mix phx.server
```
