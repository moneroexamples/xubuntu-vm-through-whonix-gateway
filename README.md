# How to set Xubuntu VM to use TOR Whonix gateway

Default [whonix](https://www.whonix.org/) workstation is
based on 32-bit Debian 8.2. This is OK for most cases, since this
Debian version is from 2015. However, the 32-bit version
of Debian may limit some use cases. For example, its difficult to use [Monero](https://getmonero.org/) on a 32-bit Linux system. Monero works best with 64-bit operating systems. Also some people may not want to use Debian, and would like to use something else, such as Xubuntu 15.10 x64.

This example shows how to set-up Xubuntu 15.10 x64 in a VirtualBox so that its internet traffic goes through Whonix Tor gateway.

**Please be aware, that not using official
Whonix workstation may reduce your security and anonymity!**

The steps here were executed on Ubuntu 14.04 x64 and VirtualBox 5.0.10, and are based on those described [here](https://www.whonix.org/wiki/Ubuntu). Probably there are better ways of doing things described below. But these steps were working for me, so hopefully they will work for you as well.


## Step 1: Download Whonix Gateway VritualBox and Xubuntu images

For Whonix, go to the Whonix [download website](https://www.whonix.org/wiki/Download)
and download the latest gateway image. At the moment of writing this, it is called `Whonix-Gateway-12.0.0.3.2.ova`.

 For Xubuntu, download `xubuntu-15.10-desktop-amd64.iso` from Xubuntu's [download
 page](http://xubuntu.org/getxubuntu/).


 ## Step 2: Import Whonix workstation into VirtualBox.

  - In VirtualBox Manager: `File->Import Appliance`.
  - Locate the `Whonix-Gateway-12.0.0.3.2.ova` downloaded.
  - Follow the prompts to import the image.

## Step 2: Install Xubuntu in the VirtualBox

  - `Machine->New` and follow the prompts. I recommend at least 2GB of RAM and at least 25 GB of disc space if you plan to use latest Monero in the VirtualBox.       
  - Install Xubuntu as you would normally do it. In this example, I called it u1555.
  - Install any software you need now (e.g.,stuff needed to compile latest Monero, vim, etc.),
   before connecting the Xubuntu to the Whonix gateway. Its much faster now, than updating
   and install everything through tor.
  - You can also install guest additions if you want, to make the interacting with the Xubuntu VM easier.


## Step 3: Change Xubuntu's network to use Whonix network

 - `Machine->Settings->Network` and change Adapter 1 from NAT into Internal Network.
 - Make sure that the Name is `Whonix`.

![Network setup](https://raw.githubusercontent.com/moneroexamples/xubuntu-vm-through-whonix-gateway/master/img/network_setup.jpg)

## Step 4: Remove dhcp auto-configuraion packages from Xubuntu

You can make VirtualBox snapshot here, just in case something does not work later on,
and you would like to undo the changes.

```bash
sudo apt-get remove isc-dhcp-client isc-dhcp-common network-manager network-manager-gnome resolvconf
```
## Step 5: Check the Whonix Gateway IP and network subnet

Start the Whonix Gateway and follow the prompts. Once done, check the gateway IP address and network mask.
I used the following command:

```bash
cat /etc/network/interfaces.d/30_non-qubes-whonix
```

![Gateway IP address](https://raw.githubusercontent.com/moneroexamples/xubuntu-vm-through-whonix-gateway/master/img/gateway_ip.jpg)

From the output, we can see that the Whonix Gateway IP is `10.152.152.10` and network mask is `255.255.192.0`.

## Step 6: Check the name of network interface in Xubuntu

```bash
ifconfig -a
```

Gives (part of the output shown):

```bash
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:73:b5:e3  
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

So the interface name is `enp0s3`.


## Step 7: Make enp0s3 use Whonix gateway

Need to set static IP for Xubuntu to use Whonix network. Thus, we need IP of the form `10.152.152.xx` where xx is more than 10. For example `10.152.152.15`.

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

Also, a name server needs to be specified.

```bash
sudo nano /etc/resolv.conf
```

and add:

```bash
# Whonix gateway IP.
nameserver 10.152.152.10
```

## Step 8: Restart Xubuntu

Make sure Whonix Gateway is working and restart the Xubuntu. After that, Xubuntu should use the Whonix Gateway for all internet traffic.

And now everything should be OK. You can start `firefox` and check your IP address at [tor check website](https://check.torproject.org/) or in a terminal using `elinks` or `lynx`.

For example:

```bash
elinks https://check.torproject.org/
```

![Tor check](https://raw.githubusercontent.com/moneroexamples/xubuntu-vm-through-whonix-gateway/master/img/tor_check.jpg)



## Monero in Xubuntu VM through Whonix Gateway

Because we use Xubuntu 64-bit there is no issues with running
latest Monero. How to compile it can be found at
[compile-monero-ubuntu-1510](http://moneroexamples.github.io/compile-monero-ubuntu-1510/).

The only problem is initial synchronization, as it will take ages
through tor. So better to download the current blockchain without tor before setting up the gateway or on other PC or VM that is not using Tor. Once the current blockchain is obtained it can be copied into Xubuntu VM.

Running the Monero node through tor does not take much bandwidth
nor is slow, as it is done in real-time as blocks are coming. The only slow thing is the initial synchronization as it requires downloading ~8GB or more of data. Also, it wont be full node, as other nodes will not be able to connect to you.


## How can you help?

Constructive criticism, code and website edits are always good. They can be made through github.

Some Monero are also welcome:
```
48daf1rG3hE1Txapcsxh6WXNe9MLNKtu7W7tKTivtSoVLHErYzvdcpea2nSTgGkz66RFP4GKVAsTV14v6G3oddBTHfxP6tU
```    

## Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */

    var disqus_config = function () {
        this.page.url = 'https://moneroexamples.github.io/xubuntu-vm-through-whonix-gateway/';
        this.page.identifier = 'xubuntu-vm-through-whonix-gateway'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };

    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//moneroexamples.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
