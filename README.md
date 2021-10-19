# Installation d'OpenMediaVault sur une carte Raspberry Pi

## Auteur

### Kevin Doolaeghe

## Installation d'OMV

1. Passage en mode super-utilisateur :
```
sudo su
```

2. Installation des sources `apt` :
```
apt-get install --yes gnupg
wget -O "/etc/apt/trusted.gpg.d/openmediavault-archive-keyring.asc" https://packages.openmediavault.org/public/archive.key
apt-key add "/etc/apt/trusted.gpg.d/openmediavault-archive-keyring.asc"
```

```
cat <<EOF >> /etc/apt/sources.list.d/openmediavault.list
deb https://packages.openmediavault.org/public usul main
# deb https://downloads.sourceforge.net/project/openmediavault/packages usul main
## Uncomment the following line to add software from the proposed repository.
# deb https://packages.openmediavault.org/public usul-proposed main
# deb https://downloads.sourceforge.net/project/openmediavault/packages usul-proposed main
## This software is not part of OpenMediaVault, but is offered by third-party
## developers as a service to OpenMediaVault users.
# deb https://packages.openmediavault.org/public usul partner
# deb https://downloads.sourceforge.net/project/openmediavault/packages usul partner
EOF
```

```
export LANG=C.UTF-8
export DEBIAN_FRONTEND=noninteractive
export APT_LISTCHANGES_FRONTEND=none
apt-get update
apt-get --yes --auto-remove --show-upgraded \
    --allow-downgrades --allow-change-held-packages \
    --no-install-recommends \
    --option DPkg::Options::="--force-confdef" \
    --option DPkg::Options::="--force-confold" \
    install openmediavault-keyring openmediavault
omv-confdbadm populate
```

3. Mise à jour du système :
```
apt update && apt upgrade
```

4. Installation d'OMV :
```
wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | bash
```

## Installation du module `omv-extras`

```
https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install
```
