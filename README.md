network setup partybox
======================

$ sudo apt-get update
$ sudo apt-get install hostapd isc-dhcp-server

$ sudo nano /etc/hostapd/hostapd.conf
 
  interface=wlan0
  ssid=partyBox
  hw_mode=g
  channel=6
  auth_algs=1
  wmm_enabled=0

