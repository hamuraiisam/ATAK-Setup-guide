### Upgrade Linux

Before doing any other installs it is neccessary to update the systems files and programs to the latest versions.

    sudo apt update && sudo apt upgrade
    
### Traccar server

If you want to track iOS devices which as of writing this guide does not have iTAK publically available you need to use another GPS tracking app which you can inject into FreeTAKservers REST API. In this case it is traccar.

Install unzip program and mysql-server

    apt update && apt -y install unzip mysql-server

Setup a mysql database

    mysql -u root --execute="ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root'; GRANT ALL ON *.* TO 'root'@'localhost' WITH GRANT OPTION; FLUSH PRIVILEGES; CREATE DATABASE traccar;"

Download the latest version of traccar server

    wget https://www.traccar.org/download/traccar-linux-64-latest.zip

unzip and start the traccar setup

    unzip traccar-linux-*.zip && ./traccar.run

Setup the config file, make sure to copy and paste all of the lines at once into the console

   

    cat > /opt/traccar/conf/traccar.xml << EOF
    <?xml version='1.0' encoding='UTF-8'?>
    
    <!DOCTYPE properties SYSTEM 'http://java.sun.com/dtd/properties.dtd'>
    
    <properties>
    
        <entry key="config.default">./conf/default.xml</entry>
    
        <entry key='database.driver'>com.mysql.jdbc.Driver</entry>
        <entry key='database.url'>jdbc:mysql://localhost/traccar?serverTimezone=UTC&amp;useSSL=false&amp;allowMultiQueries=true&amp;autoReconnect=true&amp;useUnicode=yes&amp;characterEncoding=UTF-8&amp;sessionVariables=sql_mode=''</entry>
        <entry key='database.user'>root</entry>
        <entry key='database.password'>root</entry>
    
    </properties>
    EOF


Set up traccar server to launch at Linux start

    sudo crontab -e

Paste this at the bottom of the file, then save and exit ( Ctrl + X )

    @reboot nohup sudo service traccar start

Reboot Linux

    sudo reboot

Now you can go to the admin panel at http://YOURIP:8082 and log in with user:admin pass:admin, you need to add a devices identifier in the admin panel for it to appear. In the traccar client you are using the same identifier which you set up in the admin panel and the same ip and port. Make sure to set the location accuracy to high since I have had awful results with the medium setting.

---

### TAKCAR Setup

I am using [takcar](https://github.com/Cale-Torino/Takcar "takcar") to convert the traccar data into ATAK using a php script which feeds into the FTS REST API. I am using an older version since the latest one does not work for me with the latest FTS - the only limitation of 1.0.0.4 is that  the name of the device does not show up but is a random number.

First, we need to set up a XAMPP server ( Say no to developer files option)

    wget https://www.apachefriends.org/xampp-files/7.4.12/xampp-linux-x64-7.4.12-0-installer.run
    chmod a+x xampp-linux-x64-7.4.12-0-installer.run
    sudo ./xampp-linux-x64-7.4.12-0-installer.run

Now lets set up XAMPP to start on Linux boot using crontab

    crontab -e
    
Add the following line at the bottom and then exit ( Ctrl + X )

    @reboot nohup sudo /opt/lampp/lampp start &

Navigate to the htdocs directory

    cd /opt/lampp/htdocs
    
Download and uznip the takcar zip file

    wget https://github.com/Cale-Torino/Takcar/releases/download/V1.0.0.4/Takcar_Version_1.0.0.4.zip && unzip Takcar*.zip
    mv -f /opt/lampp/htdocs/Takcar_Version*/takcar_scripts /opt/lampp/htdocs
    rm -f -d /opt/lampp/htdocs/Takcar_Version*
    
 Now you need to edit the config file according to [Takcars instructions](https://github.com/Cale-Torino/Takcar) and then you can go to http://YOURIP/takcar_scripts/ to test it out.
 
    sudo nano /opt/lampp/htdocs/takcar_scripts/config.php
   
---
