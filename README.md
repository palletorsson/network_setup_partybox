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

<pre> 
interface=wlan0
ssid=partyBox
hw_mode=g
channel=6
auth_algs=1
wmm_enabled=0 
</pre>

Setting up a DHCP Server

* $ sudo nano /etc/dhcp/dhclient.conf 

<pre> 
authoritative; #be careful with this setting
ddns-update-style none;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;

#for the wireless network on wlan0
subnet 10.10.0.0 netmask 255.255.255.0 {
range 10.10.0.25 10.10.0.50;
option domain-name-servers 8.8.8.8, 8.8.4.4;
option routers 10.10.0.1;
interface wlan0;
}
</pre>

* sudo nano /etc/default/hostapd
<pre> 
DAEMON_CONF="/etc/hostapd/hostapd.conf"
</pre>


Intermezzo
* sudo reboot and check to see that everything seem to work ok

Enable dhcp-server

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
