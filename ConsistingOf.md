For the Home Lab I will be configuring three main virtual machines: [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines), [pfSense Firewall](https://pfsense.org/download/), and 
[Metasploitable](https://sourceforge.net/projects/metasploitable/). The Kali machine will be used to attack the Metasploitable machine and the firewall is used to ensure nothing leaves the internal network that shouldn't.

The first thing to do is to create the pfSense machine which will use the following settings to start up
- Name pfsense
- Use the pfsense ISO as the image
- BSD as Type
- FreeBSD (64-Bit) as Version
- 1 GB RAM
- 1 Processor
- 5 GB Virtual Hard Disk
- Change the Network 1 to a Bridged Adapter to connect to the Internet
- Enable a second adapter on the Interal Network 

For Kali use the ```.vbox``` file to create a VM in VirtualBox. Then change the Network to Internal Network with the same name as the firewall.

For the Metasploitable machine, create a New VM using the following settings
- Name Metasploitable
- No ISO Image
- Linux as Type
- Ubuntu (64-Bit) as Version
- 2 GB RAM
- 1 Processor
- Use an Existing Virtual Hard Drive File and browse to select the Metasploitable.vmdk
- Change the Network to Internal Network with the same name as the firewall

Now to start up the VMs, always start the pfSense machine first as this acts as the protector for any possible attack trying to leave the network. Go through the default configurations until you need to reboot. 
Once you reboot, click the Devices tab and remove the ISO from the virtual drive. This allows the machine to boot from storage rather than being stuck in an installation loop. **Note: You may have to power down the machine 
and remove the ISO from Storage in the pfSense VM settings, be sure not to remove the ```.vdi```. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/feb50bc6-844e-4df4-9816-b20192273b14)

You should be now be at a UI that looks as above, displaying the two adapters you configured. Leave this VM open in the background and we will start the Kali machine.

Press Start for the Kali machine, let it boot up and then log in with the default credentials of Username: kali | Password: kali. 

For the Metasploitable VM it will be very simple as you just need to start the VM. The credentials are Username: msfadmin | Password: msfadmin.

Now all of the VMs that we currently need are up and running. We will attempt to gain a backdoor on the Metasploitable machine first from Kali.

This exploit is in vsftpd version 2.3.4 which allows an attacker to gain access to ftp by entering `:)` at the end of a username along with an invalid password. 

