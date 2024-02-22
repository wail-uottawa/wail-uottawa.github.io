---
permalink: /resources/
title: "Resources"
---



## Unofficial uOttawa LaTeX Templates

### Unofficial uOttawa Thesis LaTeX Template

The template is hosted on Github: [github.com/wail-uottawa/uo-thesis](https://github.com/wail-uottawa/uo-thesis){:target="_blank"}

### Unofficial uOttawa Beamer Template

[uOttawa-Beamer-Template-20180627.zip](/files/uOttawa-Beamer-Template-20180627.zip) Version: June 27, 2018 (Courtesy of Dr. Sébastien Touchette)

## Configuring WiFi for Raspberry Pi

If you are trying to configure a Raspberry Pi to use eduroam WiFi on uOttawa's campus, you may want to try one of the following methods. They have been tested on a Raspberry Pi 3 but they may work for other versions. 

### Connecting Through the Network Manager

This method is GUI-based and is probably faster and easier especially if you are novice to Linux.

1. Install the network manager. At the time this tutorial was written, this can be done by typing the following command at the prompt:  
`sudo apt-get install network-manager-gnome`
2. Next, you can launch the network manager GUI through the Raspberry Pi's applications menu and connect to eduroam.

### More Secure but Longer Method

This method is more advanced but more secure.

- Edit the file "/etc/wpa_supplicant/wpa_supplicant.conf" so that it looks as follows (you may need to create one if it does not exist).   
The primary source for the wpa_supplicant.conf file was: [https://github.com/oleks/eduroam-wpa_supplicant]( https://github.com/oleks/eduroam-wpa_supplicant){:target="_blank"} 

The file includes some security features for uottawa.ca users, such as the "ca_cert", and "subject_match" parameters.  This makes sure that users are presenting their username and password to a uottawa site, and not any AP calling themselves "eduroam".

The "identity" and "password" parameters also need to be changed for each user to their uoAccess credentials.  You can either use the cleartext password or the md4 hash.  The md4 hash would be preferred since it reduces the chance of "shoulder surfing" password capture. The (echo -n 'SecretPassword' \| iconv -t utf-16le \| openssl md4) comment shows how.

i.e., either password="SecretPassword" or password=hash:0ae63ba4ebb255905d883b298f40a411 

=== /etc/wpa_supplicant/wpa_supplicant.conf ===

```r
 # Copyright (c) 2014-2018 Oleks <oleks@oleks.info> 
 # 
 # Copenhagen Liberal License - v0.5 - September 6, 2015 
 
 ctrl_interface_group=root 
 ap_scan=1 
 eapol_version=2 
 update_config=0 
 
 network={ 
    disabled=0 
    auth_alg=OPEN 
    ssid="eduroam" 
    ca_cert="/etc/ssl/certs/ca-certificates.crt" 
    subject_match="uottawa.ca" 
    scan_ssid=1 
    key_mgmt=WPA-EAP 
    proto=WPA RSN 
    pairwise=CCMP TKIP 
    eap=PEAP 
    identity="login@uottawa.ca" # Edit this, 
    # ( echo -n 'SecretPassword' | iconv -t utf-16le | openssl md4 ) 
    password=hash:0ae63ba4ebb255905d883b298f40a411 # and this. 
    phase1="peaplabel=0" 
    phase2="auth=MSCHAPV2" 
 } 
```

- The wlan0 interface will then need to be configured in /etc/network/interfaces.  Here's a snippet.  Note that the wpa-conf line is indented (this has tripped up some users).
 

 === /etc/network/interfaces ===
 
 ```r
 ... 
 ...
 auto wlan0 
 iface wlan0 inet dhcp 
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf 
 ... 
 ...
 ```

- After that, you connect to eduroam either by rebooting the board or by executing the command "ifup wlan0".


### Another Tutorial

If a service account is available, then NOOBS could be preconfigured to use this. Here's a [tutorial]( https://learn.pimoroni.com/tutorial/sandyj/setting-up-a-headless-pi){:target="_blank"} on how to do so. 
