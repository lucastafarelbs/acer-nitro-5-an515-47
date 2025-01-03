# fix for 0408:4035 Quanta Computer, Inc. ACER HD User Facing
---

### To fix this in Pop! OS is not hard, but was tricky

#### Execute the following:
```
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential -y 
sudo apt install gcc-12 -y 
cd ~  # change to your home directory
apt-get source linux-modules-extra-$(uname -r)
cd ~/linux-*/drivers/media/usb/uvc 
mv uvc_driver.c uvc_driver.old
```

#### Inside the `~/linux-*/drivers/media/usb/uvc` download the driver with fix
wget https://raw.githubusercontent.com/rexionmars/Nitro5-AN515-58-WebCam-Module/master/uvc_driver_kernel_6.2/uvc_driver.c

#### Now is the tricky part 
I needed to reinstall the linux-headers and log as root

```
 sudo apt install --reinstall linux-headers-$(uname -r)
  reboot
  sudo su -
```

Once in root mode, execute the make:
```
make -j4 -C /lib/modules/$(uname -r)/build M=$(pwd) modules 
```

Also had to execute zstd:
```
zstd uvcvideo.ko
```

After this, I needed to send the compiled driver .ko and .ko.zst to the installed kernel drivers folder, but first created backup for both files

```
# backup zst
sudo cp /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko.zst  /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko.zst.old

# backup ko
sudo cp /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko  /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko.old

#send ko and zst
sudo cp -f uvcvideo.ko /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko

sudo cp -f uvcvideo.ko.zst /usr/lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/uvcvideo.ko.zst
```

#### So I loaded the module
```
sudo rmmod uvcvideo
sudo modprobe uvcvideo trace=0xffffffff
```

### reboot and test it
