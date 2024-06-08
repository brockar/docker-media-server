- [Resume](#resume)
- [Recommended](#recommended)
- [Requirements](#requirements)
  - [Install docker and docker-compose](#install-docker-and-docker-compose)
    - [Permissions](#permissions)
  - [Install the rest of apps](#install-the-rest-of-apps)
  - [Static ip](#static-ip)
- [Config to auto download.](#config-to-auto-download)
  - [General](#general)
  - [Sonarr / (TV/Series)](#sonarr--tvseries)
  - [Radarr / (Movies)](#radarr--movies)
  - [Download](#download)
  - [Enjoy your content (and seed it!).](#enjoy-your-content-and-seed-it)
- [Backup](#backup)
- [How to use](#how-to-use)
  - [Media](#media)
  - [Backup](#backup-1)
- [Extra](#extra)
  - [Portainer](#portainer)
    - [Logging In](#logging-in)
  - [laptop](#laptop)

# Resume

We are going to set up a server so you can have a **multimedia center** in your home and you can also **make your backups** of important stuff in the same place!
Multimedia:

- Movies
- TV
- Music

# Recommended

- Set this up on a laptop or cpu that you don't use and can be on all day.
- Install Debian without GUI (GNOME), this will consume less resources and you won't need it.
- For the system you only need 30gb, the rest will be for your files.
- Connect the laptop/pc via ethernet, wifi can get slow or slow down your wifi for all other uses.
- Do not open ports, use WireGuard.
- Seed the files at least 1.0 ratio.

# Requirements

- [debian/debian based distro](https://brockar.github.io/easy-download-debian/)
- [your user with sudo](https://gcore.com/learning/how-to-add-user-to-sudoers-in-debian/)
- git and curl (`sudo apt install git curl`)
- docker and docker compose [here](#install-docker-and-docker-compose).
- [Static ip](#static-ip)

---

## Install docker and docker-compose

[resource](https://docs.docker.com/engine/install/debian/).  
Run the following command to uninstall all conflicting packages:

1. Run the following command

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
sudo usermod -a -G docker $USER
```

Logout and logging

## Install the rest of apps

Download this repo

```bash
git clone https://github.com/brockar/docker-media-server.git
```

Make the folders you need.  
My directory organization is as follows:

```text
/home/user/
├── backup
└── media-server
    ├── file-b
    ├── homepage
    ├── jellyfin
    ├── jellyseerr
    ├── lidarr
    ├── media
    │   ├── movies
    │   ├── music
    │   └── tv
    ├── prowlarr
    ├── radarr
    ├── rtorrent
    ├── sonarr
    └── torrents
```

Modify the docker compose with your paths, on each `volumes:` on each container.

Here we have all ready, now just

```bash
docker compose up -d
```

U can enter into apps from homepage or `ip:port`.  
[Homepage Wiki](https://gethomepage.dev/latest/configs/settings/).

## Static ip

```bash
ip a
```

Look for enXXX: inet , for example eno1 o enp1s0: `192.168.100.X`.

Now configure to static your ip

```bash
sudo nano /etc/network/interfaces
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
  address 192.168.100.100 # Select your ip, i recommend .100, u can select from 2 to 255 but if is low, can fails.
  netmask 255.255.255.0
  gateway 192.168.100.1 # The same as your initial ip but with an 1 at the least
```

# Config to auto download.

## General

Configure **Prowlarr** (localhost:9696) to obtain the indexers (from where the torrents are obtained).  
Add some (1337x, EZTV for example) and save them.

From this page we are going to grab the Torznab Feed and the KEY API for Sonarr, Radarr and Lidarr.

Configure **ruTorrent** as you want. I recommend the "MaterialDesign" theme.  
Downloads > Default directory for downloads: /downloads/  
(localhost:8080)

## Sonarr / (TV/Series)

(localhost:8989)  
Settings > Indexers.  
Add the indexers from Jackett.  
Just select TVs, not movies.

You can also configure the rest as you wish.

## Radarr / (Movies)

(localhost:7878/)  
Do the same as in Sonar but with series.

## Download

Now you just have to add the series (Sonarr) you want to watch and the movies (Radarr) or music (Lidarr).

You can view the status of downloads in the applications themselves or in qBittorrent.

## Enjoy your content (and seed it!).

at http://localhost:8096 or on your TV / Mobile.

---

# Backup

[resource](https://reintech.io/blog/installing-configuring-samba-debian-12)

Install samba  
`sudo apt install samba samba-common-bin`

change smb.conf to share the folder

```
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
sudo nano /etc/samba/smb.conf
```

At the end of the file add:

```
[share-name]
   path = /home/user/backup
   browseable = Yes
   read only = No
   wins support= Yes
```

Finally

```
sudo systemctl restart smbd
```

and log in with your linux user.

# How to use

## Media

Search for the content you want to download in its respective application.  
Wait for it to download.  
Watch it on Jellyfin.

## Backup

To add the folder on linux, just connect to `smb://ip/share-name`.  
[Or can mount as a folder](https://jlsmorilloblog.wordpress.com/2017/06/25/montar-carpeta-de-red-en-linux/).

On Windows, in the explorer (This PC > `...`) you can:

- Connect to a media server
- Add a network location
- Map network drive

with `\\ip\share-name`

# Extra

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

You can add this `docker-compose.yml` to **App Template** and deploy.

Done!.

## laptop

If u server is a laptop, run the following commands:

`apt install vbetool`  
to screen off  
`sudo vbetool dpms off`

to screen on  
`sudo vbetool dpms on`

to close screen and dont turn off

```bash
sudo nano /etc/systemd/logind.conf
```

and change `#HandleLidSwitch=suspend` to  
HandleLidSwitch=ignore
