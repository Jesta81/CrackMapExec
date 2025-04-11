## Finding ASREPRoastable Accounts 


- The **ASREPRoast attack** looks for **users without Kerberos pre-authentication required**. 
- That means that anyone can **send an AS_REQ request to the KDC on behalf of any of those users and receive an AS_REP message.** 
- This last kind of message contains a chunk of **data encrypted with the original user key derived from its password**. 
- Then, using this message, the user password could be cracked offline if the user chose a relatively weak password. 
- To learn more about this attack, check out the module [Active Directory Enumerations & Attacks](https://academy.hackthebox.com/module/details/143/). 

- If we do not have an account on the domain but have a username list, we maybe get lucky and find an account with the option that **does not require Kerberos pre-authentication**. 
- If this option is set, it **allows us to find encrypted data that can be decrypted with the user's password.** 

- We can use the **LDAP protocol with the list of users we previously found with the option --asreproast** followed by a file name and specify the FQDN of the DC as a target. 
- We will search for each account inside the file users.txt to identify if there is a least one account vulnerable to this attack: 


### Bruteforcing Accounts for ASREPRoast 

`$ nxc ldap 10.129.204.177 -d inlanefreight.htb -u users.txt -p '' --dns-server dc01.inlanefreight.htb --dns-tcp --asreproast asreproast.out`


![CrackMapExec](/Finding-ASREPRoastable-Accounts/images/asreproast.png) 

- Based on our list, we found one account vulnerable to ASREPRoasting. 
- We can request all accounts that do not require Kerberos pre-authentication if we have valid credentials. 
- Let's use Grace's credentials to request all accounts vulnerable to ASREPRoast. 


### Search for ASREPRoast Accounts 

`$ nxc ldap 10.129.204.177 -d inlanefreight.htb -u grace -p 'Inlanefreight01!' --dns-server dc01.inlanefreight.htb --dns-tcp --asreproast asreproast-all.out`


![CrackMapExec](/Finding-ASREPRoastable-Accounts/images/asreproast-2.png) 

- Once we get all the hash, we can use **Hashcat with module 18200** and try to crack them. Let's use the **rockyou.txt wordlist** and attempt to crack those hashes: 


### Password Cracking 

`$ hashcat -m 18200 asreproast-all.out /usr/share/wordlists.rockyou.txt` 

![CrackMapExec](/Finding-ASREPRoastable-Accounts/images/hashcat.png) 

> username: linda
> password: Password123
> username: noemi
> password: Password!

- We found another way to find a valid account on the domain, which would allow us to gather much more information on the environment and perhaps start to compromise hosts or other users. 
