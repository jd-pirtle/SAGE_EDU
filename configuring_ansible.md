# Configure Ansible

This guide shows how to access the IP address of the nano, clone the **waggle-sensor/node-platforms** repository, and configure the Ansible inventory file

### SSH access steps

  1. Connect to the Jetson by following step 9 in <a href="https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#setup-headless">Initial Setup Headless Mode</a>
  2. Get IP for eth0
      1. run command `ifconfig eth0`
      
          ```bash
          eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 000.000.0.000  netmask 000.000.000.0  broadcast 000.000.0.000
          inet6 fe80::4ab0:2dff:fe5b:fe63  prefixlen 64  scopeid 0x20<link>
          inet6 0000:000:0000:0000:0000:0000:0000:0000  prefixlen 00  scopeid 0x0<global>
          inet6 0000:000:0000:0000:0000:0000:0000:0000  prefixlen 00  scopeid 0x0<global>
          inet6 0000:000:0000:0000:0000:0000:0000:0000  prefixlen 00  scopeid 0x0<global>
          inet6 0000:000:0000:0000:0000:0000:0000:0000  prefixlen 00  scopeid 0x0<global>
          inet6 0000:000:0000:0000:0000:0000:0000:0000  prefixlen 00  scopeid 0x0<global>
          ether 00:00:00:00:00:00  txqueuelen 1000  (Ethernet)
          RX packets 1371647  bytes 1819095948 (1.8 GB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 916252  bytes 87050219 (87.0 MB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
          device interrupt 151  base 0x5000
          ```

      2. The set of numbers next to inet is your ip address
          ```bash
          inet 000.000.0.000
          ```
          >Note: Write down your Jetson's ip address it is very important in the following steps
  3. Open a terminal on your computer and travel to your home directory (`cd ~` on macOS)
  4. Clone this repository using this command:
        ```
        git clone https://github.com/waggle-sensor/node-platforms.git
        ```
  5. Travel into the folder that is holding the Ansible Playbooks using this command:
        ```
        cd ~/node-platforms/nvidia_jetson
        ```
        >Note: Every time you run an ansible command make sure you are in this directory
  6. Replace `{ip}` in the `ansible_inventory` file with your Jetson's ip address using vim or any other text editor program
       - After it is replaced it should look similiar to this
           ```
           [nano:vars]
           ansible_host=0.0.0.0
           ```

### Set MAC address of the USB dongle for internal network

Your Jetson can host other computing devices or network-based sensors. You will need to get MAC address of your USB dongle and update the [udev rule](../ROOTFS/etc/udev/rules.d/10-waggle.rules). Connect the dongle to your Jetson.

To get MAC address of USB dongle,
```
ip address show eth1 | grep ether | tr -s ' ' | awk '{ print $2 }'
```

You will get MAC address of the dongle,
```
# You will probably get different MAC address than below as it is dependent on vender of the dongle
70:88:6b:8a:00:01
```

If you do not get any MAC address, please try the `ip` command with different number for eth
```
ip address show eth2 | grep ether | tr -s ' ' | awk '{ print $2 }'
```

Update the udev rule with obtained MAC address
```
# Replace {mac} with the MAC address you obtained above
sed -i 's/f8:e4:3b:\*/{mac}/g' ROOTFS/etc/udev/rules.d/10-waggle.rules
```

### Configure udev rule for Waggle agents

You may attempt to make your Jetson as a worker node to an existing Waggle node. In this case, running the Ansible script will need an extra step to configure before running the script. We will update the udev rule with the MAC address of your Jetson to make it connect to the core of the Waggle node.

To get MAC address,
```
ip address show eth0 | grep ether | tr -s ' ' | awk '{ print $2 }'
```

You will get MAC address of the dongle,
```
# You will probably get different MAC address than below as it is dependent on vender of the dongle
00:04:4b:a7:ce:4a
```

Update the udev rule with obtained MAC address
```
# Replace {mac} with the MAC address you obtained above
sed -i 's/48:b0:2d:\*/{mac}/g' ROOTFS/etc/udev/rules.d/10-waggle.rules
```
---

Once you have configured Ansible on the development computer, proceed to [Running The Ansible Playbook](./running_ansible.md)
