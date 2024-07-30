## Octopus Pro USB to CanBUS Mode

1. Assuming you already have your printer working with Klipper, issue the following commands:

>sudo apt update
>sudo apt upgrade

2. Now install Python

>sudo apt install python3 python3-pip python3-can
pip3 install pyserial

3. Change to the Klipper directory to update with the latest version

>cd ~/klipper
>git pull

4. Now install the CanBoot software called katapult form here (https://github.com/Arksine/katapult) 

>cd ~
>git clone https://github.com/Arksine/katapult
>cd ~/katapult

5. We need to configure the canboot/katapult firmware for the Octopus . First compile the firmware: 

>make clean
>
>make menuconfig

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/1.png?raw=true)

Change your processor model to the one on your octopus board - here I used STM32F466, but it may be a STM32F407 or STM32F429, etc. Make sure you get the clock reference correct. (The STM32F407 and STM32F429 uses a 8 MHz crystal whilst the STM32F446 uses a 12MHz crystal) 

Compile by entering the command: 

>make

6. Prepare the board to be flashed. Turn off everything, we first need to flash the octopus board by putting it in DFU mode.

7. If there is a jumper on  blue - remove it . Place a jumper on the purple area: 

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/2.png?raw=true)

8. Power up the octopus and press the green jumper once. (This is not time critical, can be done as soon as the LEDs on the octopus lights up, or after it has fully booted, or in between) 

Confirm you are in dfu mode by issuing the command: 

>lsusb

It should return something like this: 

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/3.png?raw=true)

We need to get the internal flash memory details by issuing the command: 

>dfu-util -l

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/4.png?raw=true)

9. Flash the bootloader to the octopus board (replace with your ID): 

>sudo dfu-util -a 0 -D ~/katapult/out/canboot.bin --dfuse-address 0x08000000:force:mass-erase:leave -d 0483:df11

If all went well, then this is the outcome: 

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/5.png?raw=true)

11. Time to set up the can0 network interface: 

>sudo nano /etc/network/interfaces.d/can0

Enter the following text: 

>allow-hotplug can0
  iface can0 can static
  bitrate 1000000
  up ifconfig $IFACE txqueuelen 1024
  
Ctr-X to exit, Y to save and enter on the name. 

12. IMPORTANT: Power off everything. 

13. Remove the jumper for dfu mode on Octopus. 

14. Power up the Octopus board only and flash klipper in order to activate the can0 interface 

>cd ~/klipper
>
>make clean
>
>make menuconfig

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/6.png?raw=true)

Change your processor model to the one on your octopus board - here I used STM32F466, but it may be a STM32F407 or STM32F429, etc. Make sure you get the clock reference correct. (The STM32F407 and STM32F429 uses a 8 MHz crystal whilst the STM32F446 uses a 12MHz crystal) 

>make

16. Flash Octopus board. 

Start by getting the serial of the board 

>ls -al /dev/serial/by-id

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/7.png?raw=true)

Now issue the following commands: 

>cd ~/katapult/scripts
pip3 install pyserial
python3 flash_can.py -f ~/klipper/out/klipper.bin -d /dev/serial/by-id/usb-katapult_stm32f446xx_430014000A50534E4E313120-if00

Ensure to use the value you got from the previous step for the id. 

When successful you should get the following: 

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/8.png?raw=true)

IMPORTANT : Reboot the board to activate the Can0 Interface

19. Obtain the UUID addresses: 

>cd ~/katapult/scripts
python3 flash_can.py -i can0 -q

![](https://github.com/emebecnc/Guides/blob/main/Octopus%20Pro%20USB%20to%20CanBUS%20Mode/media/9.png?raw=true)

The octopus board has already been flashed with klipper, thus the Application: Klipper. 

Place this in your printer.cfg

[mcu]
canbus_uuid: 6beb0175188a
