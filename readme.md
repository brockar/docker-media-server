# Sumary

# Requeriments
- [docker](https://docs.docker.com/engine/install/ubuntu/)
- [docker-compose](https://docs.docker.com/compose/install/standalone/)
- portainer (optional)

## Install docker / docker-compose
### docker
[resource](https://docs.docker.com/engine/install/ubuntu/).  

Run the following command to uninstall all conflicting packages:  
``` bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```  
  
1. Set up Docker's Apt repo:
``` bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```  
   
2. Install the Docker packages.  
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```   

3. Verify that the Docker Engine installation is successful by running the hello-world image.
```docker
sudo docker run hello-world
```

### docker-compose
[resource](https://docs.docker.com/compose/install/standalone/).

1. To download and install Compose standalone, run:

```bash
curl -SL https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```  

2. Apply executable permissions to the standalone binary in the target path for the installation.  
3. Test and execute compose commands using `docker-compose`.

### Permissions
Add your user ($USER)
```
sudo useradd $USER -G sudo
```

Add your user to the docker group
```
# Add kbs to docker group
sudo usermod -a -G docker $USER
#(logout and login)
```

# How to run
Simply download this repo. `git clone https://github.com/brockar/plex-jackett-sonarr-radarr-qb.git`
Create the paths you need (they are on `docker-compose.yaml`).
Modify the paths to your files on `docker-compose.yaml`, then run:
```
docker-compose up -d
```

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
>By default, Portainer generates and uses a self-signed SSL certificate to secure port 9443

Portainer Server has now been installed. You can check to see whether the Portainer Server container has started by running `docker ps`.

### Logging In
Now that the installation is complete, you can log into your Portainer Server instance by opening a web browser and going to:
```
https://localhost:9443 
```

You can add this `docker-compose.yaml` to **App Template** and deploy.  

Done!.

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