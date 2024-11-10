
<a href="url"><img src="/assets/pimox.png" align="center" height="400" width="400" ></a>
<br><br>
# Installing Proxmox 8.x on Raspberry Pi 5
>  Many thanks to the OP of this guide @enjikaka. I've decided to fork and submit a few updates. Progression through unlearning. Numerous guides exist but none were as comprehensive and direct. Minor updates to the `apt repo` and `apt sources` in  Step 8 and a few cosmetic things. 

##   1 - Flashing the OS

Install "RPi OS Lite 64-bit" with [Raspberry Pi Imager](https://www.raspberrypi.com/software/). It's listed under "Raspberry Pi OS (Other)"

##   2 - Network config

Assign your Pi a static IP in your router, then SSH into the Pi and launch the network config GUI with `nmtui` and adjust to the static ip.
- EXAMPLE: Static Reservation [https://forums.serverbuilds.net/t/guide-static-ip-addresses-in-pfsense-static-dhcp/5779](https://forums.serverbuilds.net/t/guide-static-ip-addresses-in-pfsense-static-dhcp/5779)

##   3 - Install updates

```sh
apt-get update
apt-get upgrade
```
Alternatively, update rpiOS via `raspi-config`
```bash
sudo raspi-config
```

##   4 - Assign a password to the `root` user

The default user in [Proxmox](https://pve.proxmox.com/wiki/User_Management) is `root`. From this point forward I will continue the installation as `root`. Run `sudo -i` to get a root prompt, then `passwd` to set your `root` password. This will be the login for the Proxmox UI. 

Alternatively:

![rootpw](/assets/root-pass1.png)

```bash
kielbasa@pimox5:~ $ sudo passwd root
New password:
Retype new password:
passwd: password updated successfully
kielbasa@pimox5:~ $ su root
Password:
root@pimox5:/home/kielbasa#
```


##   5 - Edit your host file
Backup and modify `hosts`
```bash
cp /etc/hosts /etc/hosts.bak
```

```bash
nano /etc/hosts
```
![hosts](/assets/hosts.png)
```
127.0.0.1 localhost pimox5
192.168.1.xx pimox5
::1 localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

> [!IMPORTANT]  
> Replace `192.168.1.xx` with your static IP.

##   6 - Edit your hostname


```bash
nano /etc/hostname
```
![hosts](/assets/hostname.png)

```bash
pimox5
# rpi5            <------------ Be sure to delete or comment this line!

```

##   7 - Reboot

```bash
reboot
```

##   8 - Add sources and keys

~~`echo "deb [arch=arm64] https://global.mirrors.apqa.cn/proxmox/debian/pve bookworm port">/etc/apt/sources.list.d/pveport.list`~~
```bash
echo 'deb [arch=arm64] https://mirrors.apqa.cn/proxmox/debian/pve bookworm port' | sudo tee /etc/apt/sources.list.d/pveport.list
```


~~`curl https://global.mirrors.apqa.cn/proxmox/debian/pveport.gpg -o /etc/apt/trusted.gpg.d/pveport.gpg`~~
```bash
curl -L https://mirrors.apqa.cn/proxmox/debian/pveport.gpg | sudo tee /etc/apt/trusted.gpg.d/pveport.gpg >/dev/null

```

##   9 - Update and install Proxmox

```sh
apt-get update
apt-get upgrade
apt-get full-upgrade
apt-get dist-upgrade
```

```
apt-get install ifupdown2
apt-get install proxmox-ve postfix open-iscsi chrony mmc-utils usbutils
```

##   10 - Edit your network interface
- Backup and modify `/etc/network/interfaces`
```bash
cp /etc/network/interfaces /etc/network/interfaces.bak
```

```bash
nano /etc/network/interfaces
```
![ntwk-inf](/assets/ntwk-inf.png)

```
# interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
# source /etc/network/interfaces.d/*                



auto lo
iface lo inet loopback

iface eth0 inet manual

auto vmbr0
iface vmbr0 inet static
address 192.168.1.xx/24
gateway 192.168.1.1
bridge-ports eth0
bridge-stp off
bridge-fd 0

iface eth0 inet manual
```

> [!IMPORTANT]  
> Replace `192.168.1.xx` with your static IP. <br>
> Comment out `# source /etc/network/interfaces.d/`

##  11 - Add DNS server

For me `/etc/resolv.conf` was empty. I added `nameserver 1.1.1.1` to this file.


##   12 - Reboot

Reboot your Pi again with `reboot`.

##   13 - Proxmox Installation Complete

You can now reach your Proxmox UI on `http://pimox5.local:8006` or `http://192.168.1.xx:8006` and login with the username `root` and the password you set in   11.

> [!IMPORTANT]  
> Replace `192.168.1.xx` with your static IP.

# [Post Install](/pimox-post.md)


---
##### Sources
- https://forum.proxmox.com/threads/proxmox-on-aarch64-arm64.121925/page-3
- https://www.reddit.com/r/Proxmox/comments/vpgn3f/how_to_login_pve_without_root_on_debian_after/
- https://raspberrytips.com/proxmox-on-raspberry-pi/
- https://github.com/jiangcuo/Proxmox-Port/wiki/Install-or-upgrade-from-repo
- https://tteck.github.io/Proxmox/

---
<meta name="fediverse:creator" content="@kielbasa@mastodon.social">
