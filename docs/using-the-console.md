---
layout: page
title: Using the Console
author: tyrion70
---

This document describes how to use the console. There are two ways to start a console. Either start gwan with the console option, or attach gwan to an already running node.

### Starting gwan in console mode
```
./gwan [options] console
```
starts gwan and gives you a command prompt

### Attaching gwan
```
./gwan attach
```
Running above command attaches a console to a running gwan instance. It uses the default ipc location, so if it complains about not being able to attach:
```
Fatal: Unable to attach to remote geth: dial unix [location]/gwan.ipc: connect: connection refused
```
try to start gwan first with the --ipc option to that location.
