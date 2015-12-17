# How to set Xubuntu VM to use TOR whonix gateway

Default [whonix](https://www.whonix.org/) workstation is
based on Debian 32 bit. This is ok for most cases. However, the 32bit version
of Debian may limit some uses. For example, its difficult to use
[Monero](https://getmonero.org/) on a 32-bit Linux. Monero works best
with 64-bit operating systems. Also some people may night be big fans of Debain,
and would like to use something else, such as Ubuntu 15.10. However, for use in a VirtualBox,
Xubuntu is be better because xfxe is much lighter than Unity.

This example shows how to set-up Xubuntu 15.10 x64 in a VirtualBox so that it
goes through Whonix Tor gateway.

Please be aware, that not using official
workstation may reduce your security and anonymity, and if you want highest
level of privacy, just stick with the official Whonix workstation!
Dont follow this example if you are extra paranoid.

The steps here were executed on Ubuntu 14.04 x64 and VirtualBox 5.0.10.


The steps here are based on those described [here](https://www.whonix.org/wiki/Ubuntu). However, that
tutorial can be found outdated in few places.


## Step 1: Download Whonix Gateway VritualBox and Xubuntu images

For Whonix, go to the Whonix [download website](https://www.whonix.org/wiki/Download)
and download the latest workstation image. At the moment of writing this,
 it is `Whonix-Gateway-12.0.0.3.2.ova`.

 For Ubuntu, download `xubuntu-15.10-desktop-amd64.iso` from Xubuntu's [download
 page](http://xubuntu.org/getxubuntu/).


 ## Step 2: Import Whonix workstation to VirtualBox.

  - In VirtualBox Manager: `File->Import Appliance`
  - locate the `Whonix-Gateway-12.0.0.3.2.ova` downloaded
  - follow the prompts to import the image.

## Step 3: Install Xubuntu in the VirtualBox

  - `Machine->New` and follow the prompts. I recommend at least 2GB of ram
     and at least 25 GB of disc space, if you plan to use latest monero in
     the VirtualBox.       
  - Install Xubuntu as you would normally do it.
  - Install any software you need now (stuff needed to compile latest monero),
   before connecting the Xubuntu to the Whonix gateway. Its much faster now, than updating
   and install everything through tor.
  - You can also install guest additions. But again, using non offical Whonix
  workstation can reduce your security.   


## Step 4: Change Xubuntu's network to use Whonix network.

 - `Machine->Settings->Network` and change Adapter 1 from NAT into Internal Network.
 - Make sure that the Name is Whonix.

![Network setup](https://raw.githubusercontent.com/moneroexamples/xubuntu-vm-through-whonix-gateway/master/img/network_setup.jpg)

## Step 5: Remove dhcp auto-configuraion packages from Xubuntu.

You can make VirtualBox snapshot here, just in case something does not work later on,
and you would like to undo the changes.

```bash
sudo apt-get remove isc-dhcp-client isc-dhcp-common network-manager network-manager-gnome resolvconf
```
## Step 6: Check the Whonix Gateway IP and network subnet.

Start the Whonix Gateway and follow the prompts. Once done, check the gateway IP address and network mask.
I used the following command:

```bash
cat /etc/network/interfaces.d/30_non-qubes-whonix
```

![Gateway IP address](https://raw.githubusercontent.com/moneroexamples/xubuntu-vm-through-whonix-gateway/master/img/gateway_ip.jpg)


## Step 6: Check the name of network interface in Xubuntu.

```bash
ifconfing -a
```

Gives

```bash
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:73:b5:e3  
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:360 errors:0 dropped:0 overruns:0 frame:0
          TX packets:360 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:30435 (30.4 KB)  TX bytes:30435 (30.4 KB)
```

So the interface name is `enp0s3`.


## Step 7: Make enp0s3 use Whonix gateway.

Need to set static IP for Xubuntu from Whonix network. Thus we need ip of the form
`10.152.152.xx` where xx is more than 10. For example, `10.152.152.15`.

```bash
sudo nano /etc/network/interfaces
```

add the following to the end:

```bash
auto enp0s3
iface enp0s3 inet static
       ## Increment last octet of address
       ## on optional additional workstations.

# Xubuntu static IP
address 10.152.152.15  

# Whonix Network mask
netmask 255.255.192.0  

# Whonix gateway IP
gateway 10.152.152.10  

```


## Step 8: Specify name server for Xubuntu.

```bash
sudo nano /etc/resolv.conf
```

and add:

```bash
# Whonix gateway IP.
nameserver 10.152.152.10
```

## Step 9: Restart Xubuntu

Make sure Whonix Gateway is working and restart the Xubuntu. After that, Xubuntu should use
the Whonix Gateway for all internet trafic.

And now everything should work. You can start `firefox` and check your ip address at [tor check website](https://check.torproject.org/) or in console using `elinks` or `lynx`.


## Monero in Xubuntu VM through Whonix Gateway

Because we use Xubuntu 64-bit there is no issues with running
latest monero. How to compile it can be found at
[compile-monero-ubuntu-1510](http://moneroexamples.github.io/compile-monero-ubuntu-1510/).

The only problem is initial synchronization, as it will take ages
through tor. So better to download the current blockchain without tor before setting up the gateway or on other PC or VM that is not using Tor. Once the current
blockchain is obtained it can be copied blockchain files into Xubuntu VM.

Running the bitcoin node through tor does not take much bandwitch
or is not slow, as it is done in real time as block are coming. The only slow thing is the initial synchronization at it requires
downloading ~8GB or more of date.


## How can you help?

Constructive criticism, code and website edits are always good. They can be made through github.

Some Monero are also welcome:
```
48daf1rG3hE1Txapcsxh6WXNe9MLNKtu7W7tKTivtSoVLHErYzvdcpea2nSTgGkz66RFP4GKVAsTV14v6G3oddBTHfxP6tU
```    
