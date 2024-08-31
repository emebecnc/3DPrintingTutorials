## CM68 Host Box

The control board uses the CM68 core based on RK3568 as the upper computer of Klipper. Its system is compiled based on Debian 10, and the environment and plug-ins required by Klipper are pre-installed. After burning, it can be used directly. 

![](https://github.com/emebecnc/3DPrintingTutorials/blob/main/CM68%20Host%20Board/media/1.png?raw=true)

### The OS of CM68

**Filename:** CM68_kernel5.1_lvds7_040325

**Download:** LINK

1. In order to install Debian Linux on the CM68  Host Board, download SDDiskTool_v1.69. You may want to open config.ini with notepad and change languague to English modifying the Lang1File line, it should look like this: Lang1File=English.ini. Then save it.

2. Extract the image on your Desktop or prefered folder.

3. Format your SD Card in FAT32. 

4. Open SD_Firmware_Tool, select your storage (SD Card), check SD Boot and load the image, then click on Create. This will take a few minutes. 

![](https://github.com/emebecnc/3DPrintingTutorials/blob/main/CM68%20Host%20Board/media/2.jpg?raw=true)

When the process finish, insert the SD Card on the Host Board and power it on. 

### Connect via Ethernet

The control board defaults to obtain IP automatically, make sure your router can provide DHCP service normally. You only need to plug in the Internet cable, and when you see the green and yellow lights on, indicates that it is connected to the network.

You have two methods to login the mainsail webpage:

### IP address
you need to go to your router to find the device named voron-02-pro, and write down the IP address
### Host name
The kit has already installed the required plug-ins, and you can access the mainsail page through the pre-set host name: voron-02-pro.local

### Connect via WiFi

- First connect the machine to your LAN via Ethernet, make sure you can log in to CM68 via SSH.
- Plug the included USB wifi into any USB interface on CATALYST board
- Use the following command checks whether the wifi module is recognized:
```
sudo ifconfig
```
- Use the following command to connect to wifi
```
sudo wifi_sta_start.sh ssid password
```
- Use the following command checks whether the wifi module is connected to your router, and get the ip address:
```
sudo ifconfig
```
### Wifi Connection Issues
If you are having problems with your Catalyst wifi, please fix using the following solution. 

SSH into the Catalyst, and run the following command `sudo nano /etc/network/interfaces`

Once you have run the command, add the following lines 

```
auto wlan0
iface wlan0 inet dhcp
wpa-ssid Your Network Name Here
wpa-psk Your Password Goes Here
```
example if your wifi name is fysetcwifi and password was ItRocks:
```
auto wlan0
iface wlan0 inet dhcp
wpa-ssid fysetcwifi
wpa-psk ItRocks
```

Links: 
https://www.fysetc.com/products/fysetc-cm68-host-box-board-for-klipper-3d-printer-diy

Buy Link
https://s.click.aliexpress.com/e/_DnPCw1Z
