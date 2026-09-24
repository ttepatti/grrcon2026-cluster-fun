# 2018 Ford Explorer Police Interceptor Instrument Panel Cluster (IPC)

## What is it?

This is the instrument cluster from a 2018 Ford Explorer Police Interceptor, now ready for you to hack! Ford calls this module the “Instrument Panel Cluster”, or “IPC” module to be specific.

## How does it work?
Upon being powered, the instrument cluster will light up, but nothing else will happen. This is because the instrument cluster thinks the car is off, and it is awaiting data from other modules in the vehicle!

This Ford instrument cluster receives much of its data from other modules within the vehicle, via the vehicle’s “HS3” CAN bus. This CAN bus connection allows modules to broadcast information about their sensor readings which in turn can be read and processed by the instrument cluster. The instrument cluster can then use that data to light up various status or warning indicators, move the gauge needles based on changes in vehicle speed or RPM, or display error messages on the cluster screen.

## How can I connect?

This module uses the vehicle’s “HS3” CAN bus, which runs at a speed of 500 kbps. Currently, this CAN bus is connected to a “CANable 2.0” USB to CAN adapter so that you can connect your laptop.

## Useful resources?

Many Ford vehicles share similar CAN bus structures and message formats. I have found great overlaps between this generation of Ford Explorer and the the DBC file “ford_lincoln_base_pt.dbc”, which is distributed as part of CommaAI’s opendbc project:
https://github.com/commaai/opendbc/blob/master/opendbc/dbc/ford_lincoln_base_pt.dbc

It also may be worth experimenting with unknown Arb IDs, or trying out other miscellaneous Ford CAN information that people have found online. This unit definitely hasn’t been perfectly documented on the internet, maybe you’ll find something new!

If you would like to learn more about the CAN bus, CAN-related tools, DBC files, or how CAN frames are structured, please check out [introductory-resources.md](introductory-resources.md)

## Example Messages
One of the first fun things to do is waking up the cluster. You can “start the car” (aka: trick the instrument cluster into thinking the car is running) using the CAN message transmitted on Arb ID 0x3B3, also known as “BodyInfo_3_FD1”.

This message is broadcast by the vehicle’s Gateway Module (GWM) and informs the cluster of 31 different signals! Out of those signals, the most important ones included in this CAN frame are:

  - Whether or not the key is in the ignition
  - The type of key in the ignition (this is for Ford’s “MyKey” restricted driving keys for teenagers or whatnot…)
  - The ignition status (Off, Accessory, Start, Run)

With just these three signals, we can trick the instrument cluster into thinking the car is running!

Try sending the below CAN frame repeatedly and see what happens:

```sh
cansend can0 3b3#4048001210050000
```

If all goes well, you should see the cluster’s screen turn on, lights come on, etc.!

If you would like to know more about the inner workings, I highly recommend seeking out a DBC file for Ford vehicles… OR, if you want to YOLO it, just guess bytes and see what happens!

## Helpful Hints

To start you on your journey, here are a couple of fun CAN messages I’ve found to play around with (note, these names are from the public `ford_lincoln_base_pt.dbc` file published by the opendbc project)

  - **0x3B3** - BodyInfo_3_FD1 – the vehicle’s ignition status, among other things
  - **0x167** - VehicleOperatingModes – whether or not the engine is running
  - **0x204** - EngVehicleSpThrottle – the engine’s current RPM (tachometer)
  - **0x156** - EngineData_6 - coolant temperature
  - **0x171** - EngineData_1 - the currently selected gear, PRNDL
  - **0x202** - EngVehicleSpThrottle2 – the vehicle’s current speed

Try to play around with the bytes in these!

```sh
# sent every 10ms
cansend can0 167#72800011fff9f400
cansend can0 204#c00000c15e000000
cansend can0 156#9600000003000000
# sent every 20ms
cansend can0 202#0400000060000000
# sent every 30ms
cansend can0 171#0400f00000000000
# sent every 100ms
cansend can0 3b3#4048001210050000
```

## Repeating Messages
Many CAN messages aren’t sent a single time, they are sent over and over on a repeated basis. If you’re inspecting a DBC file for information, try seeing if there is a “GenMsgCycleTime” value associated with the ID that you’re investigating. You might need to send the CAN frame every 10ms, 100ms, or 500ms.

As an example, the message to control the cluster’s speedometer is called “EngVehiclespThrottle2”, and has an Arb ID of 0x202, or 514 in decimal. If we search “ford_lincoln_base_pt.dbc” for this Arb ID, we can find the message’s “GenMsgCycleTime” value on line 5311:

```
BA_ “GenMsgCycleTime” BO_ 514 20;
```

This value of “20” means that the instrument cluster is expecting to receive an updated speedometer message every 20ms. So if you want to move the speedometer’s needle, you should repeatedly send your frame every 20ms!
