# Terra Linux for ASUS C202

Terra-Linux is a statically compiled linux-from-source rootfs for the Terra Chromebook (asus c202). The intention behind its design was to create a small footprint cli-driven environment with all the important trimmings.

Terra has no installer, there will never be an installer. There is no package manager, there will also never be a package management system or a repo. This is an as-is image intended to be used as a base to be built upon, as a rescue system or as a daily driver for embedded linux users.

There is no bootloader in the image it is ONLY a rootfs image.

An initial misconception is that this is srictly for chromebooks, that is incorrect. It will work on a vast number of Intel and AMD devices. Any of the braswell devices should work oob. Will work on legacy Intel and AMD64 hardware out of the box. Also works in vmware. Other 64 bit embedded intel systems or modern UEFI may require you importing or compiling your own kernel and intel firmware drivers.


What's in it?

    Linux 5.8.7
    Libc
    Busybox
    Net-Tools
    Wireless-Tools
    WPA_Supplicant
    Dbus
    Irssi
    Ngircd
    Lighttpd
    Tmux
    Screen
    w3m
    Gpm
    Nano
    Htop
    LMSensors
    MPlayer (plays on frame buffer with playvideo $file)
    Mencoder
    Alsa
    img2fb (my own invention, must be root or add your profile to the video group)

What isnt in it?

Any shared libraries other than libc.

## How to put the multiple image parts together

cd to the path where you've stored the files:

    Terra-R1-A.img.tar.xz.part-aa
    Terra-R1-A.img.tar.xz.part-ab
    Terra-R1-A.img.tar.xz.part-ac
    Terra-R1-A.img.tar.xz.part-ad

Run this command:

    cat *part-a* > Terra-R1-A.img.tar.xz

## Want to test it without installing? Not a problem, you don't have to install it to give it a try here is a chroot script to aid you.

**note** The resolv.conf must contain the same contents as your host system or at least the same dns adrresses as your host system.

    #!/bin/sh
    #
    # Slipy cant handle legacy boot so we do this for him
    #

    mkdir test
    mount Terra-R1-A.img test
    mount -o bind /sys test/sys
    mount -o bind /dev test/dev
    mount -o bind /proc test/proc
    cd test
    chroot .
    umount test/sys
    umount test/dev
    umount test/proc
    umount test



## How do I install it?

Your usual method for sliding a rootfs on an embedded system. Copy the contents to your own hallowed out rootfs and change the /boot/grub/grub.cfg and fstab to have matching UUIDS to your file system.

Or, if you use a legacy boot set up its simple just

    dd if=Terra-R1-A.img of=/dev/path/to/device
    
After that follow the usual steps for installing grub. Terra will take it from there.



## I'm smart enough to install it, but not smart enough to figure out the networking... pls halp?

Terra uses the regular busybox method of networking you can set your ip addressing, subnet and dns in:

    /etc/network/interfaces
    
Here is an example of a working /etc/network/interfaces config:

    # interfaces(5) file used by ifup(8) and ifdown(8)
    auto lo
    iface lo inet loopback
    auto wlan0
    iface wlan0 inet static
            address 192.168.1.175
            netmask 255.255.255.0
            gateway 192.168.1.1
            broadcast 192.168.1.1

Also, in order for wpa supplicant to function you must edit:

    /etc/wpa_supplicant/wpa_supplicant.conf

Here is what you will find:

    network={
	    ssid="mywifiap"
	    #psk="apasswordhere"
    }

Also make have a look at your firewall rules in /usr/bin/firewall, you can enable and disable the firewall with these commands. (its on by default)

    firewall #turns the firewall on
    
and...
    
    firewall off #turns the firewall off
