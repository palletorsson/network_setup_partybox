network setup partybox
======================

* $ sudo apt-get update
* $ sudo apt-get install hostapd isc-dhcp-server dnsmasq nginx
* $ sudo nano /etc/hostapd/hostapd.conf
* $ sudo nano /etc/dhcp/dhclient.conf 
* $ sudo nano /etc/default/isc-dhcp-server
* $ sudo nano /etc/network/interfaces
* $ sudo nano /etc/sysctl.conf
* $ sudo service hostapd status
* $ sudo service isc-dhcp-server status
