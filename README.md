Jubash
======

Jubash is an interactive shell environment to try out Jubatus on the command line.

Jubash is licensed under LGPL 2.1.

Requirements
------------

- Python 2.7+
- Jubatus 0.4.2+
- Jubatus Python Client (`pip install jubatus`)

Example
-------

By using `jubash`, you can easily integrate your existing workflow on the command line with Jubatus.
Here is a small example:

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

Please note that `jubash` is an casually implemented tool to roughly try out Jubatus and NOT AT ALL INTENDED FOR PRODUCTION OR SENSITIVE USE CASES.
