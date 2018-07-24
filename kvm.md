#list domains from non-root user with permission
```
virsh --connect qemu:///system list --all
```

#netconf for private bridge (br1) on host
```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp1s0
iface enp1s0 inet manual

auto br0
iface br0 inet dhcp
        bridge_ports enp1s0
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0

auto br1
iface br1 inet static
        address 172.16.0.2
        netmask 255.255.255.0
        network 172.16.0.0
        broadcast 172.16.0.255
        bridge_ports none
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0
```

#.. and for guest
```

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens3
iface ens3 inet dhcp

allow-hotplug ens6
iface ens6 inet static
        address 172.16.0.1
        netmask 255.255.255.0
        network 172.16.0.0
        broadcast 172.16.0.255
```

#attach bridge to domain
```
attach-interface --domain <domain> --persistent --type bridge --source br1
```

#list some domain network info (on host with private bridge)
```
virsh # domiflist utility
Interface  Type       Source     Model       MAC
-------------------------------------------------------
-          bridge     br0        rtl8139     52:54:00:82:45:5c
-          bridge     br1        rtl8139     52:54:00:bc:27:7b
```

#list some domain net info (on host where vms share host bridge)
```
 domiflist cuckoo
Interface  Type       Source     Model       MAC
-------------------------------------------------------
vnet0      bridge     br0        virtio      52:54:00:fb:04:df
```


#virt-install debian
```
virt-install --connect qemu:///system --name utility --memory 512 --cdrom /vmiso/debian_stretch_netboot.iso --disk size=8 --noautoconsole --os-type linux --accelerate --network=bridge:br0 --hvm --graphics vnc,port=5900,listen=127.0.0.1
```

#..again, but different
```
virt-install --name cuckoo --ram=8192 --vcpus=4 --cpu host --hvm --disk size=128 --location http://httpredir.debian.org/debian/dists/stretch/main/installer-amd64/ --graphics vnc,listen=0.0.0.0
```