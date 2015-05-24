network setup partybox
======================

I used these referances to set up the network:
* http://www.daveconroy.com/turn-your-raspberry-pi-into-a-wifi-hotspot-with-edimax-nano-usb-ew-7811un-rtl8188cus-chipset/
* http://www.daveconroy.com/using-your-raspberry-pi-as-a-wireless-router-and-web-server/
* http://raspberrypihq.com/how-to-turn-a-raspberry-pi-into-a-wifi-router/
* http://elinux.org/RPI-Wireless-Hotspot
* http://andrewmichaelsmith.com/2013/08/raspberry-pi-wi-fi-honeypot/
* https://learn.adafruit.com/setting-up-a-raspberry-pi-as-a-wifi-access-point/install-software

* Prepare your Raspberry PI with a SD card with a RASPBIAN:
https://www.raspberrypi.org/documentation/installation/installing-images/README.md
* Boot and expand filesystem, change timezone, etc and reboot
* By being on the same network as the Pi you can log into by using ssh ( user:pi password:raspberry )

Update
* $ sudo apt-get update

Install hostapd, This will allow anyone with Wi-Fi on their laptop or phone to connect to the pi using the SSID "partyBox".

* $ sudo apt-get install bridge-utils hostapd

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

Reboot 
* $ sudo reboot

Log in again see that the Pi start without errors

Tips: Check hostapd
* $ sudo service hostapd status
* $ sudo hostapd /etc/hostapd/hostapd.conf

Start, stop, restart hostapd:
* $ /etc/init.d/hostapd start
* $ /etc/init.d/hostapd stop
* $ /etc/init.d/hostapd restart

Install dnsmasq
* $ sudo apt-get install dnsmasq 
* $ sudo nano /etc/dnsmasq.conf

<pre>

log-facility=/var/log/dnsmasq.log
address=/#/192.168.0.1
interface=wlan0
dhcp-range=192.168.0.10,192.168.0.254,12h
no-resolv
log-queries
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
  address 10.0.0.1
  netmask 255.255.255.0
  broadcast 255.0.0.0

pre-up iptables-restore < /etc/iptables.rules

</pre>

* $ sudo nano /etc/sysctl.conf
* Add the following line to the bottom of the file:
<pre> 

net.ipv4.ip_forward=1

</pre>

Check isc-dhcp-server
* $ sudo service isc-dhcp-server status

Direct outside request to local webserver 
<pre>

#!/bin/sh

echo 1 > /proc/sys/net/ipv4/ip_forward

iptables -F
iptables -t nat -F
iptables -X

iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.10.1:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.10.1 --dport 80 -j SNAT --to-source 192.168.10.11

</pre>
* $ sudo iptables -t nat -A PREROUTING -s 192.168.1.0/24 -p tcp --dport 80 -j DNAT --to-destination 192.168.10.1:80
* $ iptables-save > /etc/iptables.up.rules
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"

sudo iptables -t nat -F

* $ sudo nano /etc/hosts
<pre>
* 127.0.0.1 
</pre>
