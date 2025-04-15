## Finding Kerberoastable Accounts 

The **Kerberoasting attack aims to harvest TGS (Ticket Granting Service) Tickets from a user with servicePrincipalName (SPN) values, typically a service account**. Any valid Active Directory account can request a TGS for any SPN account. Part of the ticket is encrypted with the account's NTLM password hash, which allows us to attempt to crack the password offline. To learn more about how this attack works, check out the **Kerberoasting** section of the [Active Directory Enumeration & Attacks module](https://academy.hackthebox.com/module/details/143/). 


To find the **Kerberoastable accounts**, we need to have a **valid user in the domain**, use the **protocol LDAP** with the option **--kerberoasting followed by a file name, and specify the IP address of the DC as a target on CrackMapExec**: 


### Kerberoasting Attack 

If there are any Kerberoastable accounts, CrackMapExec will display them along with the hash we need to attempt to crack offline. In our case, we will use the wordlist rockyou.txt to crack the password and the application [Hashcat](https://hashcat.net/hashcat/). For more on Hashcat usage, see the [Cracking Passwords with Hashcat](https://academy.hackthebox.com/module/details/20) module. 


`$ netexec ldap dc01.inlanefreight.htb -u grace -p 'Inlanefreight01!' --kerberoasting kerb-netexec.txt `


![Kerberoasting](/Valid-Account-Information-Gathering/Finding-Kerberoastable-Accounts/images/nxc-kerb.png) 


### Cracking the Hash 

`$ hashcat -m 13100 nxc-kerb.txt /usr/share/wordlists/rockyou.txt `

![Kerberoasting](/Valid-Account-Information-Gathering/Finding-Kerberoastable-Accounts/images/hashcat.png) 

![Kerberoasting](/Valid-Account-Information-Gathering/Finding-Kerberoastable-Accounts/images/hashcat-2.png) 

- Running Hashcat we are able to crack 1 of the hashes and get another set of credentials. We can also verify the credentials by running the smb module with the credentials. And we can see that the credentials are valid for elieser for smb. And by adding **--shares** onto the end of our command string we can see that our new user has READ & Write Privileges over the 'linux01' share. 

- Username: elieser
- Password: Passw0rd 

![Kerberoasting](/Valid-Account-Information-Gathering/Finding-Kerberoastable-Accounts/images/smb.png) 

![Kerberoasting](/Valid-Account-Information-Gathering/Finding-Kerberoastable-Accounts/images/shares.png) 
