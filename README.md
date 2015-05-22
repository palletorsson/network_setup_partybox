network setup partybox
======================

(http://www.daveconroy.com/using-your-raspberry-pi-as-a-wireless-router-and-web-server/)

* Prepare your Raspberry PI with a SD card with a RASPBIAN
* Boot and expand filesystem, change timezone, etc, reboot
* Log into the PI using ssh ( user:pi password:raspberry )

Update
* $ sudo apt-get update

Install new packages
* $ sudo apt-get install hostapd dnsmasq 

Setting up a hostadp 
* $ sudo nano /etc/hostapd/hostapd.conf 

Setting up a DHCP Server
* $ sudo nano /etc/dhcp/dhclient.conf 
<pre> mjau </pre>
Enable dhcp-server on the pi 
* $ sudo apt-get install isc-dhcp-server
* $ sudo nano /etc/default/isc-dhcp-server

Define a subnet the wireless card.
* $ sudo nano /etc/network/interfaces

* $ sudo nano /etc/sysctl.conf
* $ sudo service hostapd status
* $ sudo service isc-dhcp-server status
* 
-Setting up a Wireless Access Point
-Setting up an rPi as a Router
-Enabling IP Forwarding
