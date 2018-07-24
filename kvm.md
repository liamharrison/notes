#list domains from non-root user with permission
```virsh --connect qemu:///system list --all```

#netconf for private bridge (br1)
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