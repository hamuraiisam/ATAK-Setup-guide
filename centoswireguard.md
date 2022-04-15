### Upgrade Linux

Before doing any other installs it is neccessary to update the systems files and programs to the latest versions.

    sudo yum update && sudo yum upgrade

### Install wireguard

Use this script to install wireguard, use the command again to configure clients

    wget https://git.io/wireguard -O wireguard-install.sh && bash wireguard-install.sh
