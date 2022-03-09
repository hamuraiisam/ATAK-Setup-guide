

# ATAK-SERVER setup guide

A quick reference guide for the commands I use to setup my ATAK servers, all the commands are run on a clean image of Ubuntu 20.4

---

### Upgrade Linux

Before doing any other installs it is neccessary to update the systems files and programs to the latest versions.

    sudo apt update && sudo apt upgrade
---

### Install FTS (Automated)

Script by [lennisthemenace](https://github.com/lennisthemenace/FreeTAKServer-Installer "lennisthemenace") which sets up FTS, UI, Webmap and the video server automatically and sets them up in crontab to launch at Linux startup

    curl -L https://git.io/JLSRp > install.py ; sudo python3 install.py

These commands need to be ran after the FTS install script to fix the issues with the latest FTS version not launching properly

    pip3 install itsdangerous==2.0.1
    pip3 install markupsafe==2.0.1

Edit all the neccessary config files for FTS and then reboot Linux to launch FTS

    sudo reboot
---

### Traccar server

If you want to track iOS devices which as of writing this guide does not have iTAK publically available you need to use another GPS tracking app which you can inject into ATAK. In this case it is traccar.

Install unzip program and mysql-server

    apt update && apt -y install unzip mysql-server

Setup a mysql database, please change the password ( CHANGEME ) value

    mysql -u root --execute="ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'CHANGEME'; GRANT ALL ON *.* TO 'root'@'localhost' WITH GRANT OPTION; FLUSH PRIVILEGES; CREATE DATABASE traccar;"

Download the latest version of traccar server

    wget https://www.traccar.org/download/traccar-linux-64-latest.zip

unzip and start the traccar setup

    unzip traccar-linux-*.zip && ./traccar.run

Setup the config file, make sure to copy and paste all of the lines at once into the console, please change the password ( CHANGEME ) value

    cat > /opt/traccar/conf/traccar.xml << EOF
    <?xml version='1.0' encoding='UTF-8'?>
    
    <!DOCTYPE properties SYSTEM 'http://java.sun.com/dtd/properties.dtd'>
    
    <properties>
    
        <entry key="config.default">./conf/default.xml</entry>
    
        <entry key='database.driver'>com.mysql.jdbc.Driver</entry>
        <entry key='database.url'>jdbc:mysql://localhost/traccar?serverTimezone=UTC&useSSL=false&allowMultiQueries=true&autoReconnect=true&useUnicode=yes&characterEncoding=UTF-8&sessionVariables=sql_mode=''</entry>
        <entry key='database.user'>root</entry>
        <entry key='database.password'>CHANGEME</entry>
    
    </properties>
    EOF


Set up traccar server to launch at Linux start

    sudo crontab -e

Paste this at the bottom of the file, then save and exit

    @reboot nohup sudo service traccar start

Reboot Linux

    sudo reboot

Now you can go to the admin panel at http://YOURIP:8082 and log in with user:admin pass:admin, you need to add a devices identifier in the admin panel for it to appear. In the traccar client you are using the same identifier which you set up in the admin panel and the same ip and port. Make sure to set the location accuracy to high since I have had awful results with the medium setting.

---

### TAKCAR Setup

I am using [takcar](https://github.com/Cale-Torino/Takcar "takcar") to convert the traccar data into ATAK using a php script which feeds into the FTS REST API. I am using an older version since the latest one does not work for me with the latest FTS - the only limitation of 1.0.0.4 is that  the name of the device does not show up but is a random number.

#To be continued

---

### Self hosted Zerotier controller ( With UI ! )

Get Curl

    sudo apt install curl

Get Zerotier

    curl -s https://install.zerotier.com | sudo bash



install [Docker](https://docs.docker.com/get-docker) and [Docker Compose](https://docs.docker.com/compose/install)



https://github.com/dec0dOS/zero-ui
