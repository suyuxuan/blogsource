---
title: How to config Go debugger in VSCode for Mac
date: 2017-03-19 21:39:06
tags: 
    - Go
categories: 
    - Go
---


## 1.Installation of Go Plugin for VSCode

Search and install Go Plugin by @lukehoban on VSCode Extensions.


## 2.Installation of Debugger -- [Delve](https://github.com/derekparker/delve)

Instead of Homebrew, pls [install Delve manually](https://github.com/derekparker/delve/blob/master/Documentation/installation/osx/install.md). Or there will be some issues when you debug Go project from VSCode.



Attached sample of launch.json

``` json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch",
            "type": "go",
            "request": "launch",
            "mode": "debug",
            "remotePath": "",
            "port": 9000,
            "host": "127.0.0.1",
            "program": "${workspaceRoot}",
            "env": {},
            "args": [],
            "showLog": true
        }
    ]
}
```