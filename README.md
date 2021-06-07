netatmo - weewx driver for netatmo weather stations
Copyright 2015 Matthew Wall
Distributed under terms of the GPLv3

This driver has two modes of operation.  It can use the netatmo API to obtain
data from the netatmo servers, or it can parse the packets sent from a netatmo
station.  The latter works only with netatmo firmware 101 (circa early 2015).
Firmware 102 introduced arbitrary encryption in response to a poorly chosen
decision to include the wifi password in network traffic sent to the netatmo
servers.  Unfortunately, the encryption blocks end-users from accessing their
data, while the netatmo stations might still send information such as the wifi
password to the netatmo servers.

By default this driver will operate in 'cloud' mode.

Communication with the netatmo servers requires 4 things: username, password,
client_id, and client_secret.  The username and password are the credentials
used to access data at netatmo.com.  The client_id and client_secret must be
obtained via the dev.netatmo.com web site.  Using these 4 things, the driver
automatically obtains and updates the tokens needed to get data from the
server.

[bricebou]
This fork aims to be compatible with weewx 4.* and has been "rewritten" in Python 3 thanks to the `2to3` tool.
[/bricebou]

[jkrasinger]
This is a fork from bricebou/weewx-netatmo fork.
Changes:
* This code should work with Python 2.7 und 3.x (running with weewx<4 and weewx=4.x)
* This code was changed (deeply changed and not rewritten!!) to support the weired netatmo API on /getstation call
  which does not return all "RAIN" data. Rain data is created every five minutes, but the NETATMO API is only returning
  the data in 10 minutes intervals on the /getstation call with only the last measurement included. So we are missing
  the data in between which leads to different rain summaries in weewx/wunderground/... in opposite to the
  NETATMO statistics (i.e. daily rain will be less in weewx as on NETATMO site). The only way to get all data is using
  the /getmeasurement call. I included in addition to the /getstation call also an /getmeasurement call and add
  the additional rain data directly into the generated collection/loop record. 
* To avoid writing duplicated rain data in the 5/10 minutes interval (by default the netatmo module generates two
  LOOP records within 1 archive record generation which leads to a duplication of rain data, as the rain data is 
  accumulated by weewx.) i also added a logic for remembering already written rain data and reset them to "0" 
  in the LOOP packets if already written in the previous LOOP packet.
* ~~Added two new configuration Parameters,~~
  * ~~gm_device_id (MAC of the NETATMO main station) and~~
  * ~~gm_node_id (MAC of the "rain" Module)~~
* previous parameters (gm_device_id, gm_node_id) no longer needed  
* Autodetection of Rain Module(s)
* Handling of multiple rain modules

[/jkrasinger]



===============================================================================
Installation instructions:

1) download the driver:

wget -O weewx-netatmo.zip https://github.com/smoldersj/weewx-netatmo/archive/master.zip

2) install the driver:

sudo wee_extension --install weewx-netatmo.zip

3) select and configure the driver:

sudo wee_config --reconfigure

[bricebou]
It seems that the configuration isn't written so you have to manually edit the ``/etc/weewx/weewx.conf` file.
[/bricebou]

4) start weewx:

sudo /etc/init.d/weewx start
