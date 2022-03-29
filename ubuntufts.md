
### Upgrade Linux

Before doing any other installs it is neccessary to update the systems files and programs to the latest versions.

    sudo apt update && sudo apt upgrade
---

### Install FTS (Automated)

Script by [lennisthemenace](https://github.com/lennisthemenace/FreeTAKServer-Installer "lennisthemenace") which sets up FTS, UI, Webmap and the video server automatically and sets them up in crontab to launch at Linux startup

Install Curl

    sudo apt install curl
 
Run the FTS install script

    curl -L https://git.io/JLSRp > install.py ; sudo python3 install.py

These commands need to be ran after the FTS install script to fix the issues with the latest FTS version not launching properly

    pip3 install itsdangerous==2.0.1
    pip3 install markupsafe==2.0.1

Run this to generate the config files and then use Ctrl + C (twice) to exit

    sudo python3 -m FreeTAKServer.controllers.services.FTS 

Edit all the necessary config files for FTS (/opt/FTSconfig.yaml) and FTS-UI (/usr/local/lib/python3.8/dist-packages/FreeTAKServer-UI/config.py) by replacing all the 127.0.0.1 and 0.0.0.0 IPs with you real IP and then reboot Linux to launch FTS

    sudo reboot
