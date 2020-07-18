# memoeb_docker

## Initial MacOS set-up:
We need to use VirtualBox docker-machine image instead of desktop Docker client to forward USB device and attach to mobile process inside container (rephrase).

1. Install VirtualBox >= 6.20

2. Collect and install VirtualBox Extension Pack for your VirtualBox version, we need it for USB 2.0 support: https://download.virtualbox.org/virtualbox/6.0.20 (just change 6.0.20 in url to your version to catch a correct one).

3. Install docker-machine

  Command: 
  
  `brew install docker-machine`

4. Generate docker VM

  Command:
  
  `docker-machine create -d virtualbox default`

5. Now you need to stop docker machine to enable correct USB settings

  Command:
  
  `docker-machine stop`

6. Enable USB 2.0

Command:

`vboxmanage modifyvm default --usbehci on`

7. Start docker-machine

Command: `docker-machine start`

8. Find ProductID and VendorID for your device

Command:

`vboxmanage list usbhost`

9. Add your device using ProductID and VendorID from previous step

Command:

`vboxmanage usbfilter add 0 --target default --name 'Nexus' --vendorid YOUR_vendorid --productid YOUR_productid`
- p.s. you can also do it through VirtualBox GUI on USB settings tab.

10. Enable docker environment:

Command:

`eval "$(docker-machine env default)"`
p.s. Your VirtualBox image name might be different from "default" if you changed it on step 4.

11. Pull the image

Command:

`docker pull hd421/memoeb:updated`

## After docker set-up:

> If you are already configured docker-machine (as described in previous section). Each time you restart docker-machine there is one command to be used:

`docker-machine start`

`eval "$(docker-machine env default)"`

## Launch:

### Mac without USB-C
Command:

`docker run --rm -it --privileged -v /dev/bus/usb:/dev/bus/usb --device=/dev/bus/usb:/dev/bus/usb:rwm CONTAINER_ID_HERE`

### New Mac with USB-C

Command: 

`docker run --rm -it --privileged -v /dev/bus/usb:/dev/bus/usb --device=/dev/ttyUSB0 CONTAINER_ID_HERE`

### iOS:

Commands:

`lsusb` (check that you see your attached device)

`usbmuxd -f --verbose &` (press "Trust" on your phone)

`frida-ps -Uai` (make sure you see your apps list)


### Android:

Commands:

`lsusb` (check that you see your attached device)

`adb devices` (Make sure ADB is enabled in device settings and press "Allow USB debugging")

`frida-ps -Uai` (make sure frida-server is running on your device)
