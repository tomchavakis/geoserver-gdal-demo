### Installation Guide
### Ubuntu 16.04

This guide will hopefully get you set up with Oracle JRE 8, Tomcat 9, GeoServer 2.14.2, and GDAL 2.2 on Ubuntu 16.04 Server.

In my case I am running this tutorial in a VMWare on IP address 192.168.34.10, you may have a different IP you need to substitute.

## Install Java

```
sudo add-apt-repository ppa:linuxuprising/java
sudo apt update
sudo apt-get install oracle-java11-installer
sudo apt-get install oracle-java11-set-default
Verify Java Version
java -version
```

## Install Tomcat9

```
mkdir ~/src
cd ~/src
wget http://apache.mirror.iweb.ca/tomcat/tomcat-9/v9.0.20/bin/apache-tomcat-9.0.20.tar.gz
tar xzf apache-tomcat-9.0.20.tar.gz
sudo cp -r apache-tomcat-9.0.20 /opt/tomcat9
sudo useradd tomcat9
sudo usermod -s /bin/false tomcat9
sudo chown -R tomcat9 /opt/tomcat9
sudo -u tomcat9 /opt/tomcat9/bin/startup.sh
Tomcat should now be visible at http://192.168.34.10:8080.
```

## Install GDAL
```
$ sudo add-apt-repository -y ppa:ubuntugis/ubuntugis-unstable
$ sudo apt upgrade
$ sudo apt install gdal-bin libgdal-dev libgdal-java libgdal20 gdal-data
$ gdalinfo --version
GDAL 1.11.3, released 2015/09/16
```

## Install GeoServer 2.15.1
```
$ cd ~/src
$ wget https://liquidtelecom.dl.sourceforge.net/project/geoserver/GeoServer/2.15.1/geoserver-2.15.1-war.zip
$ sudo apt install unzip
$ unzip geoserver-2.15.1-war.zip -d geoserver-2.15.1
$ sudo cp geoserver-2.15.1/geoserver.war /opt/tomcat9/webapps/.
$ sudo chown -R tomcat9 /opt/tomcat9
```

Geoserver should now be visible at http://192.168.34.10:8080/geoserver/web. The default login is admin with password geoserver.

If you go to Data > Stores and Add new Store, you can see there are no GDAL options yet (e.g. VRT).

## Install GeoServer GDAL Plugin
```
$ cd ~/src
$ wget https://datapacket.dl.sourceforge.net/project/geoserver/GeoServer/2.15.1/extensions/geoserver-2.15.1-gdal-plugin.zip
$ unzip geoserver-2.15.1-gdal-plugin.zip -d geoserver-2.15.1-gdal-plugin
$ sudo cp geoserver-2.15.1-gdal-plugin/*.jar /opt/tomcat9/webapps/geoserver/WEB-INF/lib/.
$ sudo cp /usr/share/java/gdal.jar /opt/tomcat9/webapps/geoserver/WEB-INF/lib/.
$ sudo chown -R tomcat9 /opt/tomcat9
```

At this point GeoServer will see the plugin but will not load it (See About & Status > Server Status, then Modules and look for gs-gdal in the list). We need to set some options for Tomcat to pass to Java and GeoServer.

## Install Geoserver GRIB Plugin
```
$ cd ~/src
$ wget https://datapacket.dl.sourceforge.net/project/geoserver/GeoServer/2.15.1/extensions/geoserver-2.15.1-grib-plugin.zip
$ unzip geoserver-2.15.1-grib-plugin.zip -d geoserver-2.15.1-grib-plugin
$ sudo cp geoserver-2.15.1-grib-plugin/*.jar /opt/tomcat9/webapps/geoserver/WEB-INF/lib/.
$ sudo chown -R tomcat9 /opt/tomcat9
```

## Install Geoserver NETCDF Plugin
```
$ cd ~/src
$ wget https://datapacket.dl.sourceforge.net/project/geoserver/GeoServer/2.15.1/extensions/geoserver-2.15.1-netcdf-plugin.zip
$ unzip geoserver-2.15.1-netcdf-plugin.zip -d geoserver-2.15.1-netcdf-plugin
$ sudo cp geoserver-2.15.1-netcdf-plugin/*.jar /opt/tomcat9/webapps/geoserver/WEB-INF/lib/.
$ sudo chown -R tomcat9 /opt/tomcat9
```

## Start Geoserver Services
```
sudo -u tomcat9 /opt/tomcat9/bin/shutdown.sh
sudo -u tomcat9 GDAL_DATA=/usr/share/gdal/2.2 JAVA_OPTS="-Djava.library.path=/usr/lib/jni" /opt/tomcat9/bin/startup.sh
Now if you go to the GeoServer main page (important — do not reload the page as it won't show the new settings) and then go to the Modules list, gs-gdal should now have a checkmark and be loaded. Additionally, visiting the New data store page will show the GDAL options.
```

Author
Tom Chavakis

Official Branch:

James Badger (jpbadger@ucalgary.ca)

Some information based on the GeoServer documentation for GDAL raster coverage plugin installation.

License
This tutorial is licensed under CC BY 4.0.