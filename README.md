network setup partybox
======================

* Prepare your Raspberry PI with a SD card with a RASPBIAN:
https://www.raspberrypi.org/documentation/installation/installing-images/README.md
* Boot and expand filesystem, change timezone, etc and reboot
* By being on the same network as the Pi you can log into by using ssh ( user:pi password:raspberry )

Update
* $ sudo apt-get update

1. Install hostapd, This will allow anyone with Wi-Fi on their laptop or phone to connect to the pi using the SSID "partyBox".

* $ sudo apt-get install bridge-utils hostapd

Setting up a free wifi
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

* driver=nl80211 
* The driver you are using depends on what wireless card you use

Also:
* sudo nano /etc/default/hostapd

<pre> 
DAEMON_CONF="/etc/hostapd/hostapd.conf"
</pre>

Reboot 
* $ sudo reboot

Log in again see that the Pi start without errors

Tips: Check hostapd
* $ sudo service hostapd status
* $ sudo hostapd /etc/hostapd/hostapd.conf

Here is how to start, stop, restart hostapd:
* $ /etc/init.d/hostapd start
* $ /etc/init.d/hostapd stop
* $ /etc/init.d/hostapd restart

2. Setting up a DHCP Server, we will use dnsmasq. In this exampel we will use 192.168.10.1 as the Pi:s IP-address and a range 192.168.10.2 to 192.168.10.250 addresses to assigne to connecting computers. 

Opt 1: install dnsmasq
* $ sudo apt-get install dnsmasq 
* $ sudo nano /etc/dnsmasq.conf

<pre>

address=/#/192.168.10.1
interface=wlan0
dhcp-range=192.168.10.2,192.168.10.250,12h
no-resolv
</pre>

Check dnsmasq
* $ sudo service dnsmasq status


Define a subnet the wireless card.
* $ sudo nano /etc/network/interfaces

<pre> 
 
auto lo

iface lo inet loopback
iface eth0 inet dhcp

iface wlan0 inet static
  address 192.168.10.1
  netmask 255.255.255.0
  broadcast 255.0.0.0

pre-up iptables-restore < /etc/iptables.rules

</pre>

Direct outside request to local webserver 
<pre>

* $ sudo iptables -F
* $ sudo iptables -i wlan0 -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
* $ sudo iptables -i wlan0 -A INPUT -p tcp --dport 80 -j ACCEPT
* $ sudo iptables -i wlan0 -A INPUT -p udp --dport 53 -j ACCEPT
* $ sudo iptables -i wlan0 -A INPUT -p udp --dport 67:68 -j ACCEPT
* $ sudo iptables -i wlan0 -A INPUT -j DROP

* $ sudo sh -c "iptables-save > /etc/iptables.rules"

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

Reset Ip tables:
* $ sudo iptables -t nat -F


* $ sudo nano /etc/hosts
<pre>
* 127.0.0.1 
</pre>


* $ sudo nano /etc/sysctl.conf
* Add the following line to the bottom of the file:
<pre> 

net.ipv4.ip_forward=1

</pre>

sudo echo "Welcome! Start your Gunicorn" > /usr/share/nginx/www/index.html

Now 
* sudo reboot and check to see that everything seem to work.


I used these referances to set up the network:
* http://www.daveconroy.com/turn-your-raspberry-pi-into-a-wifi-hotspot-with-edimax-nano-usb-ew-7811un-rtl8188cus-chipset/
* http://www.daveconroy.com/using-your-raspberry-pi-as-a-wireless-router-and-web-server/
* http://raspberrypihq.com/how-to-turn-a-raspberry-pi-into-a-wifi-router/
* http://elinux.org/RPI-Wireless-Hotspot
* http://andrewmichaelsmith.com/2013/08/raspberry-pi-wi-fi-honeypot/
* https://learn.adafruit.com/setting-up-a-raspberry-pi-as-a-wifi-access-point/install-software
