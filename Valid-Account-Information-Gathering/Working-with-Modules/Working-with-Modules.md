## Working with Modules 

As we previously discussed, each CrackMapExec protocol supports modules. We can run **crackmapexec <protocol> -L** to view available modules for the specified protocol. Let's use the **LDAP protocol** as an example. 

`$ crackmapexec ldap -L `

![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/modules.png) 


```

[*] MAQ                       Retrieves the MachineAccountQuota domain-level attribute
[*] adcs                      Find PKI Enrollment Services in Active Directory and Certificate Templates Names
[*] daclread                  Read and backup the Discretionary Access Control List of objects. Based on the work of @_nwodtuhs and @BlWasp_. Be carefull, this module cannot read the DACLS recursively, more explains in the options.
[*] get-desc-users            Get description of the users. May contained password
[*] get-network               
[*] laps                      Retrieves the LAPS passwords
[*] ldap-checker              Checks whether LDAP signing and binding are required and / or enforced
[*] ldap-signing              Check whether LDAP signing is required
[*] subnets                   Retrieves the different Sites and Subnets of an Active Directory
[*] user-desc                 Get user descriptions stored in Active Directory
[*] whoami                    Get details of provided user

```

### Identifying Options in Modules 

A CrackMapExec module can have different options to set some custom values. There may be modules like **MAQ** which doesn't have any options, meaning that we can't modify its default action. To view a module's supported options, we can use the following syntax: **crackmapexec <protocol> -M <module_name> --options** 

Let's pick the **user-desc module** and see how we can interact with modules. 


![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/module-options.png) 


The **LDAP module user-desc** will query all users in the Active Directory domain and retrieve their descriptions, it will only display the user's descriptions that match the default keywords, but it will save all descriptions in a file.

Default keywords are not provided in the description. If we want to know what those keywords are, we need to look at the source code. We can find the source code in the directory **CrackMapExec/cme/modules/**. Then we can look for the module name and open it. In our case, the Python script that contains the module **user-desc is user_description.py**. Let's grep the file to find the **word keywords:** 


![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/keywords.png) 


### Retrieve User Description Using user-desc Module

`$ crackmapexec ldap 'FQDN IP' -u grace -p Inlanefreight01! -M user-desc  `

- Let's use the module and find interesting user descriptions: 

![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/user-desc.png) 


> - As we can see, it displays the description that contains the keywords key and pass, but all descriptions are saved in the log file.


### Using a Module with Options 

If we want to use a module option, we need to use the flag **-o.** All options are specified in the form of **KEY=value (msfvenom style).** In the following example, we will replace the default keywords and display values with the **keywords pwd and admin.** 

```

$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M user-desc -o KEYWORDS=pwd,admin

netexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M user-desc -o KEYWORDS=pwd,admin
SMB         10.129.181.152  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        10.129.181.152  389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
USER-DESC   10.129.181.152  389    DC01             User: Administrator - Description: Built-in account for administering the computer/domain
USER-DESC   10.129.181.152  389    DC01             User: testaccount - Description: pwd: Testing123!
USER-DESC   10.129.181.152  389    DC01             Saved 8 user descriptions to /root/.nxc/logs/UserDesc-10.129.181.152-20250411_023615.log


```

![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/module-keywords.png) 


### Querying User Membership 


**groupmembership** is another example of a module created during this training by an HTB Academy training developer, which allows us to query the groups to which a user belongs (we will discuss how to create your own modules later). To use it, we need to specify the user we want to query with the option USER.

There is a PR for this module at the time of writing this training, but it can be used if downloaded and placed in the modules folder until it gets approved. 

![CME Modules](/Valid-Account-Information-Gathering/Working-with-Modules/images/groupmembership.png) 
