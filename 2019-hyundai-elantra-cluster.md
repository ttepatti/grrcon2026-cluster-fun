# 2018 Hyundai Elantra Instrument Cluster

## What is it?

This is the instrument cluster from a 2018 Hyundai Elantra, ready for hax!

## How does it work?

Upon being powered, this instrument cluster will boot up and act as if a key has been inserted into the ignition. But from there, the rest is up to you!

This Hyundai instrument cluster receives much of its data from the vehicle's CAN buses. Messages like the vehicle speed, the vehicle's run state, etc. are all transmitted by other modules over CAN to the instrument cluster.

## How can I connect?

This specific instrument cluster has two different CAN bus connection - "CAN-B" and "CAN-C". Both of these have been connected to CANable 2.0 USB to CAN adapters, available for you to plug into!

B-CAN runs at a baud rate of 125 kbps, and C-CAN runs at a baud rate of 500 kbps.

You will know your connection is working properly if you see a constant stream of messages being sent by the instrument cluster over the C-CAN bus.

## Useful resources?

A number of Hyundai DBC files are available through [commaai's opendbc project](https://github.com/commaai/opendbc/tree/master/opendbc/dbc). These DBC files are a great starting place for learning how to craft messages to interact with the cluster!

On the other hand... you can also just fuzz it and throw random bytes at the cluster and see what happens :D
