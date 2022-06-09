---
title: "Wireless - Network Security" 
date: 2022-06-07 15:40:00 +0530
categories: [Wireless Network, Learn Hacking]
tags: [ctf,nmap,hackthebox]
image: /assets/img/wifi/wifi.jpeg
---

# Wireless Network 

### Wi-Fi ==> `Wireless Fidelity`
```bash
Made up of the word 'wireless' which means the use of waves instead of 'cables' for signal transfer.
```

### Wi-Fi Direct 
  A `peer-to-peer`   is a wireless connections that allow two devices to establish a direct Wi-Fi connection without an intermediary wireless access point, router, or internet connection. 

## Wireless Network Security

- To protects a wireless network from unauthorized and malicious access attempts.

### Wireless Security Protocol.
There are four wireless security protocols currently available.

	 1.  Wired Equivalent Privacy (WEP)
	 2.  Wi-Fi Protected Access (WPA)
	 3.  Wi-Fi Protected Access 2 (WPA 2)
	 4.  Wi-Fi Protected Access 3 (WPA 3) latest => with 
	  
### Secure your organization wireless Network

	  1. Changing Default `password` and `SSID`
	  2. Enable the router’s firewall
	  3. Enable MAC address filtering
	  4. Disable remote administration

### Wi-Fi Vulnerability.

##### The Ten Most Critical Wireless and Mobile Security Vulnerabilities

- Default Wi-Fi routers  ==> 
- Rogue Access Points  ==> Fake Access Point
- Wireless Zero Configuration  ==> Auto authentication to pre authenticted Wi-Fi network. Attacker can intrecept the SSID values
- Bluetooth exploits
		 ==> BlueSnarfing: Secretry access pictures, calenders, Phone contacts lists.
		 ==> BlueBugging: send SMS messages from a remote vulnerable phone spoofing the sender.
		 ==> BlueJacking: By renaming the name of phone, the hacker can trick victims into accepting bluetooth connections.
		 ==> BlueTooth DoS attack:  Invalid Bluetooth request to a mobile device phone can take a whole channel.
- WEP Weaknesses:
      ==> Passwords can easily be cracked using Airsnort.

## Hacking Wi-Fi.

i. To addjust transimision power in `Wi-Fi` Network
```bash
  iwconfig wlan0 txpower 30
```

ii. set `wlan0` in monitor mode

```bash
 airmon-ng start wlan0
```

iii. To view wireless traffic in your network Then. `Scan for available Wi-Fi`.

```bash
# airodump-ng mon0
 sudo airodump-ng wlan0mon
```

iv. Capturing PMKID `(Pair wise Master key ID)`

```bash
sudo hcxdumptool -i wlan0mon -o Hackers-ArisePMKID  --enable_status=1 --filterlist_ap=BE:91:B5:7E:51:79 --filtermode=2
```

### Tools

- `Fern WIFI cracker`  => scanns for available Network and attempts to hack it
-  `Wifite` => Wireless Auditor

```bash
   apt install hcxdumptool
   apt install hcxtools
   sudo wifite  => then CTRL+C and put number `CH` 
```

- RadareEye

```bash
  git clone https://github.com/souravbaghz/RadareEye
  ./radare <mac_addr><option>
```

- Options are

	   - blue Bluetooth RadareEye
	   - ble BLE radareEye
	   - wifi Wifi AP radareEye

## References.
1. [Wireless Vulns](https://www.helpnetsecurity.com/2006/06/29/the-ten-most-critical-wireless-and-mobile-security-vulnerabilities/)
2. [Wireless Vulns2](https://www.computerworld.com/article/2577244/top-10-vulnerabilities-in-today-s-wi-fi-networks.html)
3. [Wireless Vulns3](https://www.cisa.gov/uscert/ncas/tips/ST05-003)
