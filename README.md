Jubash: Jubatus Shell
=====================

Jubash is a shell environment to call [Jubatus](http://jubat.us/) APIs from the command line.

Jubash can be used as a handy tool to briefly tasting Jubatus without writing code.
You can also easily integrate Jubatus with your existing command-line workflow or command-based data sources.

Jubash is licensed under LGPL 2.1.

Requirements
------------

- Python 2.6+
- Jubatus 0.5.0+
- Jubatus Python Client (`pip install jubatus`)

Installation
------------

Using git (recommended):

```
$ git clone https://raw.github.com/kmaehashi/jubash.git
```

Using wget:

```
$ wget "https://raw.github.com/kmaehashi/jubash/master/jubash"
$ chmod +x jubash
```

Usage
=====

Using Interactive Shell
-----------------------

Basic usage of `jubash` is to use it as an interactive shell.
After starting Jubatus server (`jubaclassifier`, `jubarecommender`, ...), run `jubash` like this:

```
$ jubash
```

By default, `jubash` tries to connect to `127.0.0.1:9199`.
You can also connect to a remote server and/or a custom port:

```
$ jubash -H 192.168.1.2 -P 19199
```

`jubash --help` will give you the details of other options available.

After the connection is successfully established, you will see a prompt like this:

```
[Jubatus:classifier<>@127.0.0.1:9199] #
```

Now you can call any methods defined in [Jubatus APIs](http://jubat.us/en/api.html) as if it is a command.

```
[Jubatus:classifier<>@127.0.0.1:9199] # train male height 170 weight 60
[Jubatus:classifier<>@127.0.0.1:9199] # train male height 185 weight 65
[Jubatus:classifier<>@127.0.0.1:9199] # train female height 150 weight 50
[Jubatus:classifier<>@127.0.0.1:9199] # train female height 155 weight 45
[Jubatus:classifier<>@127.0.0.1:9199] # classify height 140 weight 40
female: 1.0111604929
male: 0.0962741076946
[Jubatus:classifier<>@127.0.0.1:9199] #
```

For the documentations for arguments of each API, use `help` command:

```
[Jubatus:classifier<>@127.0.0.1:9199] # help train
Syntax: train label datum_key datum_value [datum_key datum_value ...]
        Trains the model with given label and datum.
        Bulk training is not supported on the command line.
```

Calling from Shell Scripts
--------------------------

You can also execute `jubash` from shell scripts.
The following example illustrates how to call Jubatus API from shell scripts.

```
#!/bin/bash

# Anomaly detection from number of packets on eth0
# Note: jubaanomaly must be running on localhost:9199

NIC="eth0"

rx() { ifconfig "${NIC}" | perl -n0e 'm/RX bytes:(\d+)/; print $1'; }
tx() { ifconfig "${NIC}" | perl -n0e 'm/TX bytes:(\d+)/; print $1'; }

RX2="$(rx)" TX2="$(tx)"
while :; do
  sleep 1
  RX1="${RX2}" TX1="${TX2}" RX2="$(rx)" TX2="$(tx)"
  jubash -c "add rx $((${RX2} - ${RX1})) tx $((${TX2} - ${TX1}))"
done
```

Another tip for sysadmins is to monitor the status repeatedly:

```
$ watch -n 1 jubash -c get_status
```

Writing Jubash Script
---------------------

As with the usual shell programs, `jubash` also works as an interpreter (note the shebang line).

```
#!/usr/local/bin/jubash

# expecting jubaclassifier is already running on localhost:9199

get_config
get_status

train male height 170 weight 60
train male height 185 weight 65
train female height 150 weight 50
train female height 155 weight 45
classify height 140 weight 40
```

Limitations
===========

* Binary values cannot be specified on the command line.
