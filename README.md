SRCWOLTRGT_ARP
==============

Scan IP address of source using arping, when active send  WOL to IP address of target.

```
#!/bin/sh

#scan IP address of source using arping, when active send  WOL to IP address of target.

#General Usage Scenario:   Want client device "power on" to wake server containing media files.
#                          Cannot scan client device for activity using ping however because device 
#                          does NOT answer to pings (e.g., Xbox 360).
#                          All client devices (e.g., Xbox 360) however MUST support ARP to establish 
#                          basic connectivity to ethernet.
#                          Thus, use the ARP utility arping in this script to scan client device for activity.
#
#
#Specific Usage Scenario:  Want Xbox 360 "power on" to wake media center server, however the XBox native WOL doesn't work.
#                          Xbox 360 utilizes WOL when acting as a media center extender.
#                          The Xbox however appears to use WAN WOL (magic frame encapsulated within UDP frame)
#                          that is not recognized and routed (i.e., dropped) by many routers when coming in on a LAN port.
#                          Thus, use the ARP utility arping in this script to scan Xbox 360 for activity
#                          and start the media center server.

#Requirements:		   Router must have WOL app, e.g., ether-wake.
#                    Static IP assigned to source (client) and target (server).

#Target Device Description:  Insert Description Here, e.g., HTPC Media Center
#Source Device Description:  Insert Description Here, e.g., XBox 360

INTERVAL=5
NUMP=3
# Target is internal IP static address you want to wake
TARGET=Insert IP Address Here, e.g., 192.168.1.151
# Source is internal IP static address you want to scan if on or awake
SRC=Insert IP Address Here, e.g., 192.168.1.150
IFACE=br0
# MAC is the MAC address of the device you want to wake
MAC=Insert MAC address here, e.g., 00:1c:22:eb:ca:81
WOL=/usr/bin/ether-wake
LOGFILE="/var/log/ether-wake.log"

while sleep $INTERVAL;do

arping -c $NUMP -I $IFACE $SRC > /dev/null

if [ $? -eq 0 ]; then
#	echo "$SRC is on or awake and responding to address resolution queries" `date`>> $LOGFILE
	arping -c $NUMP -I $IFACE $TARGET > /dev/null
	if [ $? -eq 0 ]; then
#		echo "DO NOT WAKE because Target $Target is already awake" `date`>> $LOGFILE
		sleep 1
	else
#		echo "WAKE Target $TARGET at $MAC because $SRC has awaken but Target is still asleep" `date`>> $LOGFILE
		$WOL -i $IFACE $MAC
		sleep 1
	fi
else
#	echo "DO NOT WAKE Target because Source $SRC is off or asleep" `date`>> $LOGFILE
	sleep 1
fi

done
```
