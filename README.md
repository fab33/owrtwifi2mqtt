A simple shell script to detect presence of Wifi devices (smartphones, tablets, Amazon Dash Buttons, ..) and post the results via MQTT. This information can be processed in Homeautomation Systems like OpenHAB to turn down the heating when everyone left the appartment.

Installation
------------

### Install the MQTT client

Install the packages

- `mosquitto-client`
- `coreutils-nohup`

with either luci or opkg.

### Copy over the script

Use SCP to copy the presence_report script to `/usr/bin/presence_report` on the target device.
Call `chmod u+x /usr/bin/presence_report` to allow script execution.

### Add the script to rc.local

Place the following lines

- `nohup /usr/bin/presence_report event 192.168.1.2 >/dev/null 2>&1 &`
- `nohup /usr/bin/presence_report lastseen 192.168.1.2 >/dev/null 2>&1 &`

inside the `/etc/rc.local` file before the `exit 0`. You can to this via command-line or via LuCI in System -> Startup -> Local Startup. The script will be executed after reboot.

Usage
-----

After installation the following topics will be published for each WiFi device, using the _lowercase_ MAC address:

	owrtwifi/status/mac-00-00-00-00-00-00/lastseen/iso8601

Payload contains the timestamp when the device was seen in an ISO 8601 (and OpenHAB) compatible format, like this: `2017-08-25T19:29:57+0200`

	owrtwifi/status/mac-00-00-00-00-00-00/lastseen/epoch

Unix epoch in seconds

	owrtwifi/status/mac-00-00-00-00-00-00/event

Message will be `new` or `del` and is sent right after the device connected/disconnected to/from WiFi.


Credits
-------

Original idea and script from [Jupiter "belikh" Belic](http://community.openhab.org/users/belikh). Suggestion to use `iw event` from [Tom "tomdee" Denham](https://github.com/tomdee), merging these two scripts into one for easier installation by [afreof](https://github.com/afreof).  
This script follows [Oliver "owagner" Wagner](https://github.com/owagner)'s architectural proposal for an [mqtt-smarthome](https://github.com/mqtt-smarthome/mqtt-smarthome).
