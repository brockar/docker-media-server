<!--toc:start-->
- [Summary](#summary)
- [Recommended Setup](#recommended-setup)
- [Requirements](#requirements)
  - [Installing Docker and Docker-Compose](#installing-docker-and-docker-compose)
    - [Permissions](#permissions)
  - [Install the rest of apps](#install-the-rest-of-apps)
  - [Static IP](#static-ip)
- [Config auto download](#config-auto-download)
  - [General](#general)
  - [Download](#download)
  - [Enjoy your content (and seed it!)](#enjoy-your-content-and-seed-it)
- [Backup Samba](#backup-samba)
- [How to use](#how-to-use)
  - [Media](#media)
  - [Backup](#backup)
- [Extras](#extras)
  - [Nice-to-have containers](#nice-to-have-containers)
  - [Portainer](#portainer)
    - [Logging In](#logging-in)
  - [Laptop as a server](#laptop-as-a-server)
<!--toc:end-->

# Summary

This guide will walk you through setting up a server so you can have a multimedia center in your home, and you can also make backups of important files in the same place.  
Multimedia:

- Movies
- TV
- Music

# Recommended Setup

- Set this up on a laptop or computer that you don't regularly use, and can leave running all day.  
- Install Debian without GUI, this will consume less resources and you won't need it.
- You only need 30GiB for the system, the rest will be for your files.
- Connect the laptop/PC via Ethernet, Wi-Fi can get slow or slow down your Wi-Fi for all other uses.
- Do not open ports, use [WireGuard](https://www.wireguard.com/)/[Tailscale](https://tailscale.com/).
- Seed the files at least 1.0 ratio.

# Requirements

- [debian/debian based distro](https://brockar.github.io/easy-download-debian/)
- [your user with sudo](https://gcore.com/learning/how-to-add-user-to-sudoers-in-debian/)
- git and curl (`sudo apt install git curl`)
- docker and docker compose [here](#installing-docker-and-docker-compose).
- [Static IP](#static-ip)

---

## Installing Docker and Docker Compose

[resource](https://docs.docker.com/engine/install/debian/).  
(u can try [podman](https://podman.io/))  

Run the following command to uninstall all conflicting packages:

1. Installation

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

2. Verify that the Docker Engine installation is successful by running the hello-world image.

```bash
sudo docker run hello-world
```

### Permissions

Add your user to the docker group

```bash
sudo usermod -aG docker $USER
```

Log out and log back in.

## Install the rest of apps

Download this repo

```bash
git clone https://github.com/brockar/docker-media-server.git
```

Create the necessary folders.  
Here is an example directory structure.  

```text
/home/user/
├── backup
├── data
│   ├── media
│   │   ├── movies
│   │   ├── music
│   │   └── tv
│   └── torrents
└── docker
    ├── file-b
    ├── homepage
    ├── jellyfin
    ├── jellyseerr
    ├── lidarr
    ├── prowlarr
    └── sonarr
```

Use `create_subfolders.sh` to auto create this paths.

Modify the Docker Compose file by specifying your paths in the `volumes:` section of each container.
Or use`.env.example` with your path.

Here we have all ready, now just

```bash
docker compose up -d
```

U can enter into apps from homepage or `ip:port`.  

I use Homepage to collect all the links to the pages.
[Homepage Wiki](https://gethomepage.dev/latest/configs/settings/).

## Static IP

```bash
ip a
```

Look for the interface `enXXX: inet` , for example eno1 o enp1s0: `192.168.100.X`.

Now, configure your IP to be static

```bash
sudo vim /etc/network/interfaces
```

from

```bash
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp1s0
iface enp1s0 inet dhcp
```

to

```bash
# The loopback network interface
auto lo
iface lo inet loopback

#The primary network interface
auto enp1s0
iface enp1s0 inet static
  address 192.168.100.100 # Select your ip, i recommend .100, you can select from 2 to 255 but if is low, can fails.
  netmask 255.255.255.0
  gateway 192.168.100.1 # The same as your initial ip but with an 1 at the least
```

# Config auto download

## General

Configure **Prowlarr** ([localhost:9696](localhost:9696)) to obtain the indexers (from where the torrents are obtained).  
Add some (1337x, EZTV for example) and save them.

From this app, we are going to sync our trackers in Sonarr, Radarr, and Lidarr. You need to configure that.

Configure **qBittorrent/ruTorrent** as you want.
I recommend the "MaterialDesign" theme for ruTorrent and VueTorrent for qBittorrent.  
Downloads > Default directory for downloads: data/torrents/  
[localhost:8080](localhost:8080)

**Sonarr / (TV/Series)**
[localhost:8989](localhost:8989)  

**Radarr / (Movies)**
[localhost:7878](localhost:7878/)  

Set up both following the recommendations from [TrashGuide](https://trash-guides.info/).

## Download

Now you just have to add the series (Sonarr) you want to watch and the movies (Radarr) or music (Lidarr).

You can view the status of downloads in the applications themselves or in qBittorrent/ruTorrent.

## Enjoy your content (and seed it!)

at <http://localhost:8096> or on your TV / Mobile.

---

# Backup Samba

[resource](https://reintech.io/blog/installing-configuring-samba-debian-12)

Install samba  
`sudo apt install samba samba-common-bin`

Modify the `smb.conf` file to share the folder

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
sudo nano /etc/samba/smb.conf
```

At the end of the file add:

```bash
[share-name]
   path = /home/user/backup
   browseable = Yes
   read only = No
   wins support= Yes
```

Finally

```bash
sudo systemctl restart smbd
```

Finally, log in using your Linux user from Linux/Windows/Android/iOS.

# How to use

## Media

Search for the content you want to download in its respective application.  
Wait for it to download.  
Watch it via Jellyfin.

## Backup

To add the folder on Linux, just connect to `smb://ip/share-name`.  
[Or can mount as a folder](https://jlsmorilloblog.wordpress.com/2017/06/25/montar-carpeta-de-red-en-linux/) .

On Windows, in the explorer (This PC > `...`) you can:

- Connect to a media server
- Add a network location
- Map network drive

with `\\ip\share-name`

# Extras

## Nice-to-have containers

In the repository, there are several containers that might be useful to you.  
I'll share their respective websites, and you can run them simply like this:  

```bash
docker compose -f container.yml up -d
```

- [filebrowser](https://filebrowser.org/installation) (a file browser)
- [glances](https://glances.readthedocs.io/en/latest/docker.html) (resource viewer)
- [pi-hole](https://pi-hole.net/) (ad-blocker dns)
- [speedtest](https://github.com/alexjustesen/speedtest-tracker) (auto speedtest)

## Portainer

[resource](https://docs.portainer.io/start/install-ce/server/docker).  
A management tool for Docker.  
First, create the volume that Portainer Server will use to store its database:

```
docker volume create portainer_data
```

Then, download and install the Portainer Server container:

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

> By default, Portainer generates and uses a self-signed SSL certificate to secure port 9443

Portainer Server has now been installed. You can check to see whether the Portainer Server container has started by running `docker ps`.

### Logging In

Now that the installation is complete, you can log into your Portainer Server instance by opening a web browser and going to:

```
https://localhost:9443
```

You can add this `compose.yml` to **App Template** and deploy.

Done!.

## Laptop as a server

If your server is a laptop, run the following commands:

`apt install vbetool`  
to screen off  
`sudo vbetool dpms off`

to screen on  
`sudo vbetool dpms on`

to close screen and prevent the system from turning off.

```bash
sudo nano /etc/systemd/logind.conf
```

And change `#HandleLidSwitch=suspend` to  
`HandleLidSwitch=ignore`
