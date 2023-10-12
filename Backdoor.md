The first thing we need to do start off, is to ensure that the pfSense machine is online fully and that both Kali and Metasploitable are running. 

Open a terminal in Kali and run the command `sudo netdiscover`, this will show all of the IP addresses that are currently being used on the network (our interal network we created). It will take a second or two and will
display the two connections we currently have. One being the firewall and the other being the Metasploitable machine. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/b2f23932-7ce1-4e93-9922-2fb277a71a89)

If you open the pfSense VM you can check the interface to see what the LAN IP address is connected to, in my case it is `192.168.1.1`. Now you know that the Metasploitable machine must be the other IP address since we only 
have two other VMs running. Open a web browser within Kali and type the IP address of the Metasploitable machine which should bring you to a webpage like shown below. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/9683e6c6-a9b4-4a0a-86af-e4b7bfa53987)

Back in the command line we will use netcat to exploit the misconfigured FTP. Remember the syntax for the netcat command which is `nc (destination ip) port`. The standard port for FTP is 21.
In my case it is `nc 192.168.1.100 21`

Once in the FTP service we use the `user Username:)` and `pass password`. Both of these values are arbitrary as it only matters that we use `:)` at the end of the username. Once the password is entered,
the terminal will look unresponsive but we should now open a new terminal window, leaving that one open. In the new window, we will be using another netcat command to connect to the Metasploitable machine over port 6200. 
Use `nc -v (destination ip) 6200` where destination is the Metasploitable machine and -v is used for a more verbose output. You can see the various flags associated with netcat [here](https://www.commandlinux.com/man-page/man1/netcat.1.html)

Once entered, type `ls` to view the files associated and you can also use `whoami` to verify the status, which should be root, now that we have established a way into the Metasploitable VM. 

![image](https://github.com/JMacPort/HomeLab/assets/145376972/84b56e24-a204-4baf-b277-e9e5fa58c2ee)

From here we could compeletely delete all of the files from the VM which we won't be doing but we will execute a reboot just to show our control over the system using `reboot`. We will then lose connection to the 
Metasploitable machine as it is now offline so both Kali terminals will return to their original state. The first attack is now completed. 
