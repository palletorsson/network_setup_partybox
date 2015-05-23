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
driver=nl80211
ssid=partyBox
hw_mode=g
channel=6
auth_algs=1
wmm_enabled=0 
</pre>

Setting up a DHCP Server

* $ sudo apt-get install isc-dhcp-server
* $ sudo nano /etc/dhcp/dhclient.conf 

<pre> 
authoritative;
ddns-update-style none;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;

subnet 192.168.42.0 netmask 255.255.255.0 {
  range 192.168.42.10 192.168.42.254;
  option broadcast-address 192.168.42.255;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
  option routers 192.168.42.1;
  interface wlan0;
}
</pre>

* sudo nano /etc/default/hostapd

<pre> 
DAEMON_CONF="/etc/hostapd/hostapd.conf"
</pre>

Intermezzo
* sudo reboot and check to see that everything seem to work.

Define a subnet the wireless card.
* $ sudo nano /etc/network/interfaces

<pre> 
 
auto lo

iface lo inet loopback
iface eth0 inet dhcp

iface wlan0 inet static
        address 192.168.42.1
        netmask 255.255.255.0
        broadcast 255.0.0.0
        
pre-up iptables-restore < /etc/iptables.rules
</pre>

* $ sudo nano /etc/sysctl.conf
* $ sudo service hostapd status
* $ sudo service isc-dhcp-server status

Setting up a Wireless Access Point
Setting up an rPi as a Router
-Enabling IP Forwarding
