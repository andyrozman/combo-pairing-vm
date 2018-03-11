# Accu-Chek/Roche Combo pairing with use of Virtual Machine 

## What is this about

This is extension (or other option) of [Gregory Bel's solution](https://github.com/gregorybel/combo-pairing) (in further text GBS). Please read that document first (it contains much more details than this one). This is are mostly just step-by-step instructions, on how I did it, without "Phone 1" and with VM instead.

## Prerequistes

- You need to be doing this on Linux (it might also work on Mac (OS X) or any other unix/linux flavour), because we will change Mac address of BT Dongle
- Working BT Dongle (about 75% of them should allow you to change MAC address on them)


## What you need

- Phone you want to use with your pump, what ever the Android version (in GBS phone 2), in further text phone
- Phone needs to be rooted
- Ruffy needs to be installed
- Developer mode and debug over USB activated

NOTE: While this operations shouldn't do any damage to your phone, please backup it before continuing.



## Now lets get to bussiness

### Getting MAC from your Phone

You need to copy/write down Bluetooth MAC of your phone. (Settings/About phone/State/Bluetooth address). 


### Create working Lineage OS 14.1 Virtual machine


1. Download VirtualBox and correct Extensions for it. Add your user to vboxusers group - we will need to see all USB devices from host computer. You will probably need to retart vbox services and machine.
2. Download Lineage OS image, you need at least R1 (not RC1, that one doesn't work). You can use this link for [Lineage 14.1 R1](https://www.fosshub.com/Android-x86.html/cm-x86-14.1-r1.iso) or go to [Android x86 site](http://www.android-x86.org) and get it from there 
3. Now create new virtual machine, you can go with defaults (set 2Gb of RAM, everything else by default). Attach the ISO with Lineage and you are ready to go. When first grub menu appears, select Installation. Detailed instructions can be found on youtube see [link](https://www.youtube.com/watch?v=WnpOL-Gvsxs) (You can follow his steps, just use our image and you can ignore the steps with SD Card)
4. After install is done, start the VM and wait...wait...wait... First start always takes quite a few minutes (can be up to half hour, depending on speed or your system). 
5. If you have problem with mouse, disable mouse integration (Input->Mouse integration) and mouse will start working
6. You will be shown steps how to connect to your google account, you can skip thoose, unless you want to continue using image later on.


### Set MAC address to your USB Dongle

Like I said before, about 75% of Dongles, should allow you to change MAC address (CSR - Cambridge Silicon Radio own 75% of market, so if you have their chipset you are OK, others you will just have to try)

1. Install bluez (if not installed already) and bluez-utils/bluez-tools (if available) (google how to do that for your flavour of OS)
2. Run command **bdaddr**. If command is found skip to step 4.
3. Download sources for the same version of bluez you have installed (some flavours don't include tools in prepackaged install). Unpack the file and build it. bdaddr will be in tools directory after your build is done (./configure and make)
4. Run **hciconfig**, it will show you all BT devices you have, the one with highest X (hciX) should be the Dongle.
5. Note your currect MAC address, so that you can change it back later
6. Shutdown your phone (that has the same MAC we are trying to set)
7. Run command to change MAC (use MAC of your phone)

    ``` bdaddr -i hci1 AA:BB:CC:DD:EE:FF ```

   You will get message "Address changed - Reset device now" if operation was succesful. Just unplug the BT Dongle and plug it in again. If you run hciconfig again Dongle should have new MAC


### Now for the fun part - Pairing the combo

1. Leave BT Dongle attached to your computer
2. Start VM with Lineage
3. When system is started, check that your BT is attached (Devices -> USB, should show all USB devices on your system, even integrated ones, if they are not there then either your user is not in vboxusers group or you have some other issue). If you can't get it to work google it, until your BT Dongle is visible you are stuck).
4. Now system is running with BT attached, you can click on Bluetooth in your Android configuration (in VM) it will come up.
5. Download your version of ruffy (you should build from branch combo from [Milos Kozak's Repo](https://github.com/MilosKozak/ruffy)
	
	- You can either put ruffy.apk somewhere on internet and download it from there (internet in VM should be working Out-of-the-box)
	- or you can switch into Terminal mode (Alt+F1 Terminal mode, Alt+F7 GUI Mode) and get it with scp from your host machine.
		- when you issue command **ifconfig**, you will get listing of all your adapters, look at IP of eth0, it should be something like 10.0.2.15. If 10.0.2.15 is your virtual machine, then your host will be 10.0.2.2
		- install openssh-server into your host (if you don't have it already), alternativelly you could use any external server (that has ssh installed)
	    - now just ```scp username@10.0.2.2:~/ruffy.apk .```
	    
6. Start ruffy and pair your Combo with VM (if you want to have nice name in Pump, configure it in Bluetooth configuration beforehand (Renanme this device))
7. Pairing should be succesful, if not put the pump nearer to BT Dongle (when name is displayed on Pump, don't press button at once, wait about 5 seconds)
8. Now we just have copy the files to somewhere we can use them

	a) Switch to Terminal mode (Alt-F1)
	
	b) Copy bluetooth pairing data:
	- ```cd /data/misc/bluedroid```
	- ```scp bt_config.conf username@server:~/some_directory``` (username on server, server could be 10.0.2.2 (local one) or some other one, ~/some_directory means this will be put into some_sirectory that resides in home directory of username)
		
	c) Copy ruffy data:
	- ```cd /data/data/org.monkey.d.ruffy.ruffy```
	- ```zip -r ../ruffy_data.zip *```
	- ```scp ruffy_data.zip username@server:~/some_directory```
	
9. You can shutdown VM (run ```halt now``` from terminal)	

### Now to not so fun part

Now we need to get data we gathered to our phone. There are two steps to do: 
	1. add bluetooth pairing data
	2. copy ruffy configuration data

How to do that is described in [Gregory Bel's solution](https://github.com/gregorybel/combo-pairing/README.md). Look for text "Connect phone 2 to PC" and follow instructions from there.


If you have any questions you can find me arround github or gitter (andyrozman)
