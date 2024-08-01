## Prereq

A pc :)

## Installing

First you want to prepare a image for proxmox, you can use balena etcher for that.
- Proxmox iso: https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso
- balenaEtcher: https://etcher.balena.io/

For proxmox setup there's nothing in particular you should for the most part is basicly a OS installation like any other.

Once you have installed Proxmox you can start provisioning your VMs. Since we're most intrested here in installing portainer for a streaming server I'd recommend choosing to install your new VM into a big HDD you don't want to change very soon, with proxmox changing HDDs can be very easy but with this particular setup my portainer started to misbehave so I wouldn't recommend you to do that. Creating new directories in proxmox if very simple, I'd recommend to use that to configure a new hard driver to be used.

Time for our VM, I'd suggest you install ubuntu server: https://ubuntu.com/download/server. Basicly you have to upload this ISO to your proxmox then you can use it to create the VM.

The only thing while installing the VM:
- Dont select LVM
- Remember to install ssh when asked

Once your VM is up and running you can connect to it using any other PC on your home network, this is very convinient so you don't have to keep other devices such as monitors and keyboards attached to your server. In order to connect to your new VM note down its ip
and connect using some SSH client such as putty for windows, you'll have to use your VM's user and password to login.

For portainer setup:
Those commands I took from pi-hosted github page, I only changed the portainer version cause I had some issues with running as is.

```bash
sudo mkdir -p /portainer/Files/AppData/Config/portainer || error "Failed to create the Portainer Config Folder"

sudo docker pull portainer/portainer-ce:latest || error "Failed to pull latest Portainer docker image!"
sudo docker run -d -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /portainer/Files/AppData/Config/portainer:/data portainer/portainer-ce:2.20.3 --logo "https://pi-hosted.com/pi-hosted-logo.png" || error "Failed to run Portainer docker image!"
```

This will start portainer, once you finish the setup you want to update App Templates with: https://raw.githubusercontent.com/pi-hosted/pi-hosted/master/template/portainer-v2-amd64.json

Now you want to install Sonnar, Raddar, Jacket, Jellyfin and Transmission. Most of the configuration for those apps should be very straighforward I'll mention somethings that I had issue with:
* It's easier to just configure Sonnar, Raddar and transmission all to either use data folder or downloads folder.
* I had accessing rights problem with both Sonnar and Raddar, when I tried to add a new movie or series it was not finding the TV or MOVIES folder, using good old chmod 777 in those folder solved the issue.
* I had to configure Jacket with Gluteon because depending on where you live jacket may have problem to fetch the lists. Below is the docker compose file(you have to put these in Stack):
```docker

services:
  gluetun:
    image: qmcgaw/gluetun
    cap_add:
      - NET_ADMIN
    environment:
      - VPN_SERVICE_PROVIDER=nordvpn
      - VPN_TYPE=openvpn
      - OPENVPN_USER=***
      - OPENVPN_PASSWORD=***
      - SERVER_COUNTRIES=United States
    ports:
      #- 8080:8080 #qbittorent
      - 9117:9117 #jackett
      #- 8989:8989 #sonarr
      #- 7878:7878 #radarr
      #- 8686:8686 #lidarr
      #- 9696:9696 #prowlarr
  
  jackett:
    image: linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - PATH=/lsiopy/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
      - PS1=$(whoami)@$(hostname):$(pwd)\$
      - HOME=/root
      - TERM=xterm
      - S6_CMD_WAIT_FOR_SERVICES_MAXTIME=0
      - S6_VERBOSITY=1
      - S6_STAGE2_HOOK=/docker-mods
      - VIRTUAL_ENV=/lsiopy
      - LSIO_FIRST_PARTY=true
      - XDG_DATA_HOME=/config
      - XDG_CONFIG_HOME=/config
      - TMPDIR=/run/jackett-temp
    volumes:
      - /../config:/portainer/Files/AppData/Config/Jackett
      - /../downloads:/portainer/Downloads
    restart: unless-stopped
    network_mode: service:gluetun
    depends_on:
      - gluetun

```
As you can see you can use gluetun with other services, maybe a day I'll configure they all under gluetun but today is not this day :) 

## License

[MIT](https://choosealicense.com/licenses/mit/)
