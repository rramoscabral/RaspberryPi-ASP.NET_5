# RaspberryPi-ASP.NET_5
How to run ASP.NET 5 on a Raspberry Pi 2

The following instructions allow you to install ASP.NET on 5 Raspberry Pi using the minimum resources to monetize the remaining resources for web applications or other desired features.

The following instructions were made using a Microsoft Windows operating system, you can use Linux or Mac OS X with WineHQ (https://www.winehq.org/).

Image used: MINImal raspBIAN image for Raspberry Pi (2015-11-12)
https://minibianpi.wordpress.com/download/
   - Raspbian “Jessie” based
   - Kernel 4.1.7+ #817
   - 14 secs boot (on RPi 2B)
   - 29 MB RAM used
   - 451 MB disk space used
   - Fit on 1GB SD Card
   - Optimized ext4 file system with swap disabled
   - Support for RPi B, RPi B+ and the new RPi 2B
   - Targeted for embedded or server applications (NAS, Web server, electronic applications)
   - 100% full compatbile with official release
   - DHCP client enabled
   - SSHD enabled
   - root user enabled (default password: raspberry – please change it a.s.a.p.)

MD5: b8546c547cdc8030c3d0bf03af707c1f
SHA1: 0ec01c74c5534101684c64346b393dc169ebd1af
   
 
Firts format your SD whit SDFormatterv 4.0 avaible at https://www.sdcard.org/downloads/formatter_4/
Then install the image using 'Win32 Disk Imager' a tool for writing images to USB sticks or SD/CF cards avaible at http://sourceforge.net/projects/win32diskimager/.

With the recorded image on the SD card can now put the card in Raspberry and on.

Login data by default ROOT user:
User: root
Password: raspberry

First configure the operating system to use all SD card space. You must first install the Raspi-config:

# apt-get update
# apt-get install raspi-config

With raspi-config installed can run the same:

# raspi-config

elect the "Expand Filesystem" option. You will be informed that the file system has been changed and select "OK" and then "Finish" will be asked whether to restart select "YES".

With the Raspberry restarted start the session with the Root and check the address of the local ip address in order to connect through ssh with PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/) that is an SSH client, and make all operations remotely. You can also check the IP address when the Raspberry starts.

#ifconfig eth0

It is recommended to all installation and setup in the temporary area:

# cd /tmp

Update certificates:

# certmgr -ssl -m https://go.microsoft.com
# certmgr -ssl -m https://nugetgallery.blob.core.windows.net
# certmgr -ssl -m https://nuget.org
# certmgr -ssl -m https://www.myget.org/F/aspnetvnext/
# mozroots --import --machine --sync

Now install ASP.NET 5 RC:

# apt-get update
# apt-get upgrade
# apt-get install curl
# apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
# echo "deb http://download.mono-project.com/repo/debian wheezy main" | tee /etc/apt/sources.list.d/mono-xamarin.list
# apt-get install mono-complete
# apt-get install ca-certificates-mono
# apt-get install libunwind8 gettext libssl-dev libcurl4-openssl-dev zlib1g libicu-dev uuid-dev unzip
# curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh && source ~/.dnx/dnvm/dnvm.sh

Now install the libuv for Kestrel
Libuv is a multi-platform asynchronous IO library that is used by Kestrel, a cross-platform HTTP server for hosting ASP.NET 5 web applications.

# apt-get install make automake libtool curl
# curl -sSL https://github.com/libuv/libuv/archive/v1.4.2.tar.gz | tar zxfv - -C /usr/local/src
# cd /usr/local/src/libuv-1.4.2
# sh autogen.sh
# ./configure
# make
# make install
# rm -rf /usr/local/src/libuv-1.4.2 && cd ~/
# ldconfig
# cd /tmp

You can check the .NET Version Manager with the command

# dnvm

----------------------- Information on the screen -----------------------
    ___  _  ___   ____  ___
   / _ \/ |/ / | / /  |/  /
  / // /    /| |/ / /|_/ /
 /____/_/|_/ |___/_/  /_/

.NET Version Manager - Version 1.0.0-rc2-15545
By Microsoft Open Technologies, Inc.

DNVM can be used to download versions of the .NET Execution Environment and manage which version you are using.
You can control the URL of the stable and unstable channel by setting the DNX_FEED and DNX_UNSTABLE_FEED variables.

Current feed settings:
Default Stable: https://www.nuget.org/api/v2
Default Unstable: https://www.myget.org/F/aspnetvnext/api/v2
Current Stable Override: <none>
Current Unstable Override: <none>

Use dnvm [help|-h|-help|--help]  to display help text.

-------------------- End of Information on screen --------------------

Now install the latest stable version of DNX (.NET Execution Environment).

# dnvm install latest -r coreclr -g
# dnvm upgrade -r mono


You can see the currently installed DNX versions with dnvm list:

# dnvm list

----------------------- Information on the screen -----------------------

Active Version              Runtime Architecture OperatingSystem Alias
------ -------              ------- ------------ --------------- -----
  *    1.0.0-rc1-update1    mono                 linux/osx       default
       1.0.0-rc1-update1    coreclr x64          linux

-------------------- End of Information on screen --------------------

If you want to change the runtime:

# dnvm use 1.0.0-rc1-update1 -r mono

If you want to setting default:

# dnvm alias default 1.0.0-rc1-update1 -r mono


Experiment with the demonstration project available on GitHub.

# apt-get install git
# cd /tmp
# git clone https://github.com/aspnet/Home.git
# cd Home/samples/latest/HelloWeb

Clears the package cache.
# clear-http-cache

Restore packages for app based on  project.json:
# dnu restore

Build 
# dnu build

Command to launch the web application:
# dnx run

----------------------- Information on the screen -----------------------

root@minibian:/tmp/Home/samples/1.0.0-rc1-update1/HelloWeb# dnx web
Hosting environment: Production
Now listening on: http://*:5004
Application started. Press Ctrl+C to shut down.

-------------------- End of Information on screen --------------------

Now you can experience in your web browser to the IP address of the Raspberry Pi and the port that is listening for example http://192.168.1.68:5004/.


You can experiment with your project by sending the files by SFTP:

Download the latest FileZilla Client version for your operating system from https://filezilla-project.org/
Launch FileZilla and go to File > Site manager.
Fill in the IP address, user name and password of your Raspberry Pi in the dialog and choose SFTP (Port 22) as the protocol.
Click Connect and you will see the home folder of the user.


