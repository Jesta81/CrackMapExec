## Basic SMB Reconnaissance

- The SMB protocol is advantageous for recon against a Windows target. Without any authentication, we can retrieve all kinds of information, including:

1. IP Addresses 
2. Windows version
3. Fully qualified Domain Name (FQDN)
4. SMB version
5. Target local name
6. Architecture (x86/x64)
7. SMB signing enabled 

![SMB Recon](/Basic-SMB-Recon/images/cme.png)

![SMB Recon](/Basic-SMB-Recon/images/nxc.png) 

- Just for grins I also run netexec (nxc) and it gives the same output just with more color. 

#### Info
- 10.129.204.177 - dc01.inlanefreight.local - Windows 10
- 10.129.204.151 - ws001.eagle.local - Windows 10
- 10.129.205.157 - NIX-NMAP-DEFAULT - Windows 6.1 
- 10.129.205.250 - DESKTOP-VJF8GH8 - Windows 10 / Server 2019

#### All hosts with SMB Signing Disabled

- CrackMapExec has the option to extract all hosts where SMB signing is disabled. This option is handy when we want to use [Responder](https://github.com/lgandx/Responder) with [ntlmrelayx.py from Impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) to perform an SMBRelay attack. 

`$ crackmapexec smb 10.129.1.0/23 --gen-relay-list relaylistOutputFilename.txt`

![SMB Recon](/Basic-SMB-Recon/images/no-sign.png) 
