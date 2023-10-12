Setting up this attack took much longer than expected as I ran into a very small issue that was a quick fix. This lab utilizes the `dsniff` program which must be installed but for whatever reason my DNS settings were misconfigured.
Instead of reconfiguring the DNS like what should happen, I reinstalled pfSense, messed with the adapters, reconfigured a bunch of things, just to remember to run `nslookup google.com` to see that my DNS server was configured to be
the same thing as my default gateway. Anyways, the fix for this is to change the servers by using `vim /etc/resolv.conf` and then adding the DNS server above the default gateway address using `nameserver 8.8.8.8`. Or an alternative solution
is to open the settings of pfSense in a web browser on Kali, and add `8.8.8.8` to the options. 

Now, you should properly update your Kali distribution using `sudo apt update && sudo apt upgrade`. Once these are completed, verify again with `sudo apt update`. This should be done until all packages are up to date. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/03c0d093-a193-4c65-897a-01ea4ab4721d)

To start this attack once again ensure that pfSense is the first VM turned on then start Kali and Metasploitable. Now that we are all updated, use `sudo apt install dsniff` to get the tool that will be used to execute the ARP spoof.

Then execute `netdiscover -r (your-subnet)`. Since I was messing with the various adapters within pfSense I changed my LAN to run on the 172.16.0.0/16 address space. Which turns my command into `netdiscover -r 172.16.0.0/16`.

![image](https://github.com/JMacPort/HomeLab/assets/145376972/e5f6e4f4-ac7c-42a2-a963-8cf5f6da3dbd)

Since we know that `172.16.1.1` will be the pfSense VM, we know that Metasploitable is running on the other address.

Now, instead of using `sudo` we will change to be the root user using `sudo -i` and entering your password. We need to do this to enable port forwarding on the Kali machine with the command `echo 1 > /proc/sys/net/ipv4/ip_forward` command. 
There will be no output but now the Kali machine is able to forward packets that will be sent to the victim in order to fool them into thinking you are the router. This works by telling the victim that the Kali machines MAC address is mapped to 
the routers IP address. So now the victim will think the Kali machine is the router and will update it's ARP table to reflect the new changes.

Using the command `arpspoof -i eth0 -t 172.16.1.11 172.16.1.1` we will be generating fake ARP replies updating the victims ARP table. Broken down, `arpspoof` is telling Kali what program you would like to run, `-i eth0` is specifying which 
interface you are sending the requests from (the MAC address that is being remapped), `-t 172.16.1.11` specifies the target IP and `172.16.1.1` is the IP that is being mapped to the interface that was supplied.  

![image](https://github.com/JMacPort/HomeLab/assets/145376972/6046fc81-e089-478f-9549-f8e88da89616)

Breaking down the output given, the first MAC address (`8:0:27:cb:7e:f5`) is the Kali machines eth0 MAC address, the second MAC address (`8:0:27:d0:4e:c4`) is the victims MAC, `0806` is the ARP packet, `42` is the size of the packet, `arp reply 172.16.1.1 is-at` is 
telling the victim what the associated MAC address is and finally `8:0:27:cb:7e:f5` is the Kali MAC again which is now mapped to `172.16.1.1` on the victims machine. Leaving the `arpspoof` command running open a new terminal. 

In this new terminal, we will be doing the reverse of the last step, tricking the router into believing the Kali machine is the victim. So, using the same command but reversing the IP's `arpspoof -i eth0 -t 172.16.1.1 172.16.1.11`. Which
will give very similar output to the previous command.

![image](https://github.com/JMacPort/HomeLab/assets/145376972/be211cbf-34b6-4b71-9932-1f2495599c7c)

With both terminals still actively running, open a new terminal, swap to root user and issuse the command `urlsnarf -i eth0`. This will now watch out of the interface supplied for any requests that normally would go to router, but since we are spoofing
as the router on both machines we will be able to get any request that the Metasploitable VM requests. On the Metasploitable machine, ensure you are logged in and type `wget www.google.com` give the Kali terminal a few seconds to grab the request. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/221a1089-42ee-42cc-a28a-4e21f3cb3c7d)

Once the terminal updates, we now see that the requests have been grabbed and seen by our Kali machine. While we can only see a URL here this works for all unecrypted traffic where the victim would send a request. This would mean that we would be able to
inject packets with malicious or otherwise unwanted code in order to modify the attackers request. Issuing `ctrl+c` reverts the ARP tables back the original states which disconnects the Kali machine from the MITM attack. 












