# wink-mqtt

Enable local-control of Rooted Wink Hub running 3.x firmware with MQTT. Integrates nicely with Home Assistant MQTT lights, switches, sensors, etc. https://home-assistant.io/

## Installation

wink-mqtt is compiled into a single standalone shell script. Download it with curl and run the `install` command:

```
[root@flex-dvt ~]# curl -skL <release url> > /opt/wink-mqtt
[root@flex-dvt ~]# chmod +x /opt/wink-mqtt
[root@flex-dvt ~]# /opt/wink-mqtt install
```

The installer will ask for a mqtt server address (if one is not set), store a config file, set up rsyslog to launch wink-mqtt, and restart rsyslogd.

## Configuration
wink-mqtt looks for a `/etc/wink-mqtt` config file, which is auto-generated by `wink-mqtt install`. It may export the following environment variables (default values shown):

```bash
# The MQTT server
bishbosh_server='test.mosquitto.org'
# The MQTT client id
bishbosh_clientId='winkmqtt'
# A topic prefix for all wink hub mqtt devices
winkmqtt_topicBase='home'
# The full path to `aprontest` command
winkmqtt_aprontestPath='/usr/sbin/aprontest'
```

## How do I root the Wink Hub?
Matt Carrier has a good article on rooting and getting SSH access to the Wink Hub with the UART method https://mattcarrier.com/post/hacking-the-winkhub-part-1/
https://www.exploitee.rs/index.php/Wink_Hub%E2%80%8B%E2%80%8B

## How wink-mqtt works
wink-mqtt uses the `aprontest` binary on the Wink Hub to communicate with the Z-Wave, Zigbee and Lutron radios. Each paired device is given a MasterID. wink-hub subscribes to an MQTT server for `set` events. Topics are organized by `home/<masterid>/<attribute>/set`

```
$ mosquitto_pub -t 'home/20/3/set' -v 'TRUE'
```

To view MasterID and attributes you can run `aprontest -l` then `aprontest -l -m 20`.

wink-mqtt receives device status changes from rsyslogd and then queries the apron database via `aprontest` for the current values of the devices. Those updates are then published back to the MQTT topic `home/<masterid>/<attribute>` with their current values.

## Why the Wink Hub?
It's cheap, rootable and has Z-Wave, Zigbee, Lutron, Wifi and Bluetooth radios.

Please feel free to contribute, this is working but by no means a perfect solution.
