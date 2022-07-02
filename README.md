# OpenMediaVault installation on Raspberry Pi 4

## Auteur

Kevin Doolaeghe

## References

* [Github repository ⇢ OpenMediaVault](https://github.com/openmediavault/openmediavault)
* [Github repository ⇢ OMV-Extras](https://github.com/OpenMediaVault-Plugin-Developers/packages)
* [Github repository ⇢ OMV-Install-Script](https://github.com/OpenMediaVault-Plugin-Developers/installScript)
* [OMV 6 Installation](https://forum.openmediavault.org/index.php?thread/39490-install-omv6-on-debian-11-bullseye/)
* [Apache Guacamole (remote access manager)](https://guacamole.apache.org/)
* [Apache Guacamole installation process](https://www.wundertech.net/how-to-setup-apache-guacamole-on-a-raspberry-pi/)
* [Pi-hole (Advertisement blocker)](https://www.it-connect.fr/pi-hole-un-bloqueur-de-pubs-pour-tout-votre-reseau/)
* [Heimdall (Application dashboard)](https://hub.docker.com/r/linuxserver/heimdall)
* [Plex (Media center)](https://hub.docker.com/r/linuxserver/plex)
* [Jellyfin (Media center)](https://hub.docker.com/r/jellyfin/jellyfin)
* [Portainer (Docker containers management)](https://www.portainer.io/).

## Installation

* Update system packages :
```
sudo apt update ; sudo apt upgrade
```

* Download and launch OMV installation's script :
```
sudo wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```

* Install OMV-Extras module :
```
sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | sudo bash
```

## Application templates (`docker-compose`) for Portainer

* Plex :
```
---
version: "2.1"
services:
  plex:
    image: lscr.io/linuxserver/plex
    container_name: plex
    network_mode: host
    environment:
      - PUID=998
      - PGID=100
      - VERSION=docker
    volumes:
      - /var/plex/config:/config
      - /srv/dev-disk-by-uuid-1CCA-0E9F/Images:/images
    restart: unless-stopped
    privileged: true
```

* Heimdall :
```
---
version: "2.1"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall
    container_name: heimdall
    environment:
      - PUID=998
      - PGID=100
      - TZ=Europe/Paris
    volumes:
      - /var/heimdall/config:/config
    ports:
      - 8888:80
      - 8889:443
    restart: unless-stopped
    privileged: true
```

* Netdata :
```
version: '3'
services:
  netdata:
    image: netdata/netdata
    container_name: netdata
    hostname: omv
    ports:
      - 19999:19999
    restart: unless-stopped
    cap_add:
      - SYS_PTRACE
    security_opt:
      - apparmor:unconfined
    volumes:
      - netdataconfig:/etc/netdata
      - netdatalib:/var/lib/netdata
      - netdatacache:/var/cache/netdata
      - /etc/passwd:/host/etc/passwd:ro
      - /etc/group:/host/etc/group:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc/os-release:/host/etc/os-release:ro
    privileged: true

volumes:
  netdataconfig:
  netdatalib:
  netdatacache:
```

* Guacamole :
```
version: "2"
services:
  guacamole:
    image: oznu/guacamole:armhf
    container_name: guacamole
    volumes:
      - postgres:/config
    ports:
      - 8080:8080
    restart: unless-stopped
    privileged: true

volumes:
  postgres:
    driver: local
```

* Jellyfin :
```
---
version: "2.1"
services:
  jellyfin:
    image: lscr.io/linuxserver/jellyfin
    container_name: jellyfin
    environment:
      - PUID=998
      - PGID=100
      - TZ=Europe/Paris
      - JELLYFIN_PublishedServerUrl=192.168.0.5 #optional
    volumes:
      - /var/jellyfin/config:/config
      - /var/jellyfin/images:/data/images
      - /var/jellyfin/series:/data/series
    ports:
      - 8096:8096
      - 8920:8920 #optional
      - 7359:7359/udp #optional
      - 1900:1900/udp #optional
    restart: unless-stopped
    privileged: true
```
