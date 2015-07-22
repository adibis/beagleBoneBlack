# Beagle Bone Black

## Initial Setup

This setup is for a Linux host. After you connect your BBB, lsusb will show something such as, 

    Bus 001 Device 019: ID xxxx:xxxx Netchip Technology, Inc. Linux-USB Ethernet/RNDIS Gadge

You would also see, from dmesg|tail,

    [91386.683797] usb 1-1.1: new high-speed USB device number 19 using ehci-pci
    [91386.772054] cdc_ether 1-1.1:1.0 usb0: register 'cdc_ether' at usb-0000:00:1a.0-1.1, CDC Ethernet Device, <MAC_ID>

That means the BBB is recognized. You would also see a new entry for usb0 under ifconfig

    usb0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500

Everything looks good until now. Next, run

    ifconfig usb0 192.168.7.1

This will put the usb0 interface on the host machine on the same network as the BBB (which is configured as 192.168.7.2). If you ping the BBB now, it should respond to the pings. You can also SSH over USB now. If you want to enable internet sharing over USB then run the following, as root, on the BBB.

    /sbin/route add default gw 192.168.7.1
    echo "nameserver 8.8.8.8" >> /etc/resolv.conf

You can add these lines to /usr/bin/g-ether-load.sh to run it automatically on boot. Then on the host machine, allow the IPV4 packet forwarding to enable internet connection shating.

    sudo iptables -A POSTROUTING -t nat -j MASQUERADE
    echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward > /dev/null

Hopefully that would be enough to both access BBB using SSH and share internet connection with the host.
