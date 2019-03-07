---
layout: page
title: Using the Console
author: tyrion70
---

The console is a simple command line interface for interacting with your
Wanchain node and the Wanchain network, intended to be used for testing or
making simple manual transactions. The console can be very useful when
developing as it gives you a way to quickly make one-off commands.

There are two ways to start up a console: either start `gwan` with the
`console` option, or use the `attach` command to attach to an already existing
node.

### Start gwan in console mode

To start the node and open a console in one step, use the `console` command.

```
$ ./gwan [options] console
```

### Attach gwan

To attach to an already established node, use the `attach` command.

```
./gwan attach
```

The above command attaches a console to a running gwan instance found at the
default ipc location. If it is unable to connect to the default ipc, you can
specify the location of the ipc using the `--ipc` option.

```bash
$ ./gwan attach --ipc /my/path/gwan.ipc
```

Alternatively, you can connect by passing the address and port of the host.

```bash
$ ./gwan attach http://127.0.0.1:8545
```
