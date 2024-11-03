---
title: pimox post install
date: 2024-10-31 00:55:00 +0500
categories: [proxmox, pimox, raspberry pi 5]
tags: [rpi5, howto, pve]
---

# Post Install

## Glorious Proxmox Helper Script
### Post Install Script
- [https://tteck.github.io/Proxmox/](https://tteck.github.io/Proxmox/)  

![pve-post](/assets/pve-post-install.png)


```bash
# Run as root
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```

### LXC Images
[https://stevetech.me/posts/find-arm64-lxc-templates](https://stevetech.me/posts/find-arm64-lxc-templates)





##### Sources
[https://tteck.github.io/Proxmox/](https://tteck.github.io/Proxmox/)
