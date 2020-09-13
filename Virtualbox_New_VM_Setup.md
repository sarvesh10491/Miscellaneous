# Pre VM creation

### Network adapter settings for new Virtualbox VN

- Adapter 1 : Bridged  <-- To connect via SSH utility(Putty etc.) from host.
- Adapter 2 : NAT      <-- To connect to internet from VM.


# Post VM creation
```sh
sudo apt update && upgrade
```

### Get SSH server
---
```sh
sudo apt install openssh-server
sudo systemctl status ssh
sudo ufw allow ssh
```


### Setting static IP & also using internet over NAT :
---
- Modify a relevant netplan network configuration yaml file within `/etc/netplan/` directory with below.
- Change IP address for VM as applicable based on host Gateway IP.

network:
```
    version: 2
    renderer: networkd
    ethernets:
        enp0s8:
            addresses: []
            dhcp4: yes
            dhcp6: yes
            optional: true
        enp0s3:
            dhcp4: no
            dhcp6: no
            addresses: [192.168.1.100/24]
            nameservers:
                addresses: [8.8.8.8, 8.8.4.4]
```

You should be able to SSH to VM from host with static IP after running below commands in VM.
``netplan --debug apply`
`reboot`


`ifconfig -a` command should display two ethernet sockets.
- enp0s3 => Bridged
- enp0s8 => NAT

```
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
        [...]

enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.3.15  netmask 255.255.255.0  broadcast 10.0.3.255
        [...]
```



### To change hostname:
---
`hostnamectl set-hostname newHostName`
`reboot`
