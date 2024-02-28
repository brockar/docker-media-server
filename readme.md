- [Resume](#resume)
- [Recommended:](#recommended)
- [Requirements](#requirements)
  - [Install CasaOS](#install-casaos)
  - [Install the rest of apps](#install-the-rest-of-apps)
  - [Static ip](#static-ip)
- [Config to auto download.](#config-to-auto-download)
  - [General](#general)
  - [Sonarr / (TV/Series)](#sonarr--tvseries)
  - [Radarr / (Movies)](#radarr--movies)
  - [Download](#download)
- [Enjoy your content (and seed it!).](#enjoy-your-content-and-seed-it)
- [How to use](#how-to-use)
  - [File sharing and backup](#file-sharing-and-backup)
- [Extra](#extra)
  - [Portainer](#portainer)
    - [Logging In](#logging-in)
  - [Install docker and docker-compose](#install-docker-and-docker-compose)
    - [docker](#docker)
    - [docker-compose](#docker-compose)
    - [Permissions](#permissions)
  - [laptop](#laptop)

# Resume

We are going to set up a server so you can have a **multimedia center** in your home and you can also **make your backups** of important stuff in the same place!
Multimedia:

- Movies
- TV
- Music

# Recommended:

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
- docker and docker compose.
- static ip

---

## Install CasaOS

[CasaOS](https://casaos.io/) is a open source personal could system.  
CasaOS auto install docker, but If u don't want CasaOS, [Install docker and docker compose](#install-docker-and-docker-compose).

To install CasaOS just run

```bash
curl -fsSL https://get.casaos.io | sudo bash
```

and it will install all auto.

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
    ├── jackett
    ├── jellyfin
    ├── lidarr
    ├── media
    │   ├── movies
    │   ├── music
    │   └── tv
    ├── portainer
    ├── qb
    ├── radarr
    ├── sonarr
    ├── torrents
    └── wireguard
```

Modify the docker compose with your paths, on each `volumes:` on each container.
Modify also wg-easy password and host.

---

Here we have all ready, now just

```bash
docker-compose up -d
```

U can enter into apps from CasaOS or `host:port`.

## Static ip

---

# Config to auto download.

## General

Configure Jackett (localhost:9117) to obtain the indexers (from where the torrents are obtained).  
Add some (1337x, EZTV for example) and save them.

From this page we are going to grab the Torznab Feed and the KEY API.

Configure qbitorrent to download at the speeds you want at the times you want.  
(localhost:8080)

Configuration > Speed > Alternative speed limits.
Select the speeds.
Check " Schedule the use of alternative rate limits " and select the schedules.

Save it.

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

Now you just have to add the series (Sonarr) you want to watch and the movies (Radarr).

You can view the status of downloads in the applications themselves or in qbittorrent.

# Enjoy your content (and seed it!).

at http://localhost:32400/web/
or on your TV / Mobile.

---

# How to use

## File sharing and backup

On CasaOS, **Files** app u can select and share folders on your network (it uses **samba**).  
I **recommend** use `/home/user/` and not `/DATA/`.

---

# Extra

## Portainer

[resource](https://docs.portainer.io/start/install-ce/server/docker).  
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

You can add this `docker-compose.yaml` to **App Template** and deploy.

Done!.

## Install docker and docker-compose

### docker

[resource](https://docs.docker.com/engine/install/debian/).

Run the following command to uninstall all conflicting packages:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

1. Set up Docker's Apt repo:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Install the Docker packages.

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verify that the Docker Engine installation is successful by running the hello-world image.

```bash
sudo docker run hello-world
```

### docker-compose

[resource](https://docs.docker.com/compose/install/standalone/).

1. To download and install Compose standalone, run:

```bash
curl -SL https://github.com/docker/compose/releases/download/v2.24.6/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

2. Apply executable permissions to the standalone binary in the target path for the installation.
3. Test and execute compose commands using `docker-compose`.

### Permissions

Add your user to the docker group

```bash
sudo usermod -a -G docker $USER
```

Logout and logging

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
