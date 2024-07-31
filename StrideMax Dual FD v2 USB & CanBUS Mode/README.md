## StrideMax Dual FD v2 - USB Mode

Step by step installation guide to flash the Stridemax board with USB

1.  Enter to your raspberry via SSH trough MobaXTerm

2.  To compile the micro-controller code, start by running these commands on the Raspberry Pi: 

```
cd ~/klipper/ 
make clean
make menuconfig
```

3. Then select Raspberry Pi RP2040

![](https://github.com/emebecnc/Guides/blob/main/StrideMax%20Dual%20FD%20v2%20USB%20&%20CanBUS%20Mode/media/5.png?raw=true)

4. Run ```make```

5. This will output a file into klipper/out called “klipper.uf2”

6. Place the jumper between DC5V and USV

![](https://github.com/emebecnc/Guides/blob/main/StrideMax%20Dual%20FD%20v2%20USB%20&%20CanBUS%20Mode/media/4.png?raw=true)

7. Next step is to plug in the StrideMax board into the computer by holding the BOOTSEL button. This will open a new window in your computer like an external drive, but this is the RP2040.

8. Delete what’s inside and copy the file “klipper.uf2” to it. The windows will automatically close. This means that our board is flashed.

9. Back in console and with the board connected through USB, we will make a search of our devices with this command: 
```
ls /dev/serial/by-id/*
```
Which will return the MCU address

10. Add it to your printer.cfg 

```
StrideMax Dual 
[mcu stridemax_dual_X] 
serial: /dev/serial/by-id/usb-Klipper_rp2040_E6612C771F892629-if00 – REPLACE WITH YOUR SERIAL
```

11. Copy the config from GitHub according to your needs https://github.com/FYSETC/Stridemax_Dual_FD/tree/main/klipper

## StrideMax Dual FD v2 - CanBUS Mode

Step by step installation guide to flash the Stridemax board with CanBUS

1. With Katapult firmware installed, cd into Katapult path 
```
cd ~/katapult
```

2.	Compile the RP2040 with Katapult firmware

```
make clean
make menuconfig
```

![](https://github.com/emebecnc/Guides/blob/main/StrideMax%20Dual%20FD%20v2%20USB%20&%20CanBUS%20Mode/media/1.png?raw=true)

Ensure you have the correct processor model - load at the onboard chip. Also ensure RX and TX pins. CAN Bus speed as well.

3. Compile by entering the command: 

```
make -j4
```

4. Time to flash the  StrideMax (RP2040)

In order to do this, the board needs to be put in dfu mode. Press BOOTSEL and RESET.

```
lsusb
```

Your output should be:

![](https://github.com/emebecnc/Guides/blob/main/StrideMax%20Dual%20FD%20v2%20USB%20&%20CanBUS%20Mode/media/2.png?raw=true)

The RP2040 is listed as ID  2e8a:0003. 

5. Issue the command to flash Katapult: 

```
make flash FLASH_DEVICE=2e8a:0003
```
6. Power off and power on the printer

7. Flashing Klipper on the StrideMax (RP2040)

```
cd ~/klipper
make clean
make menuconfig
```
![](https://github.com/emebecnc/Guides/blob/main/StrideMax%20Dual%20FD%20v2%20USB%20&%20CanBUS%20Mode/media/3.png?raw=true)

8. Compile by entering the command:

```
make –j4
```

9. Get the UUID with one of this commands and write it down:

```
cd ~/katapult/scripts
python3 flash_can.py -i can0 –q
```
```
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```

10. Perform Flash

```
~/klippy-env/bin/python3 ~/katapult/scripts/flash_can.py -i can0 -f ~/klipper/out/klipper.uf2 -u <UUID>
```
# ADVISE: 
I have not successfully flashing Klipper with the command line on this board, so the best you can do in order to flash Klipper firmware, is after Step 8, refer to Step 6 from the USB Flashing above.

Connection Test:

```
~/klippy-env/bin/python ~/klipper/klippy/console.py -c can0 <UUID>
```
Place this into your printer.cfg

```
[mcu stridemax_dual_X]
canbus_uuid: 1d286453666e
```
