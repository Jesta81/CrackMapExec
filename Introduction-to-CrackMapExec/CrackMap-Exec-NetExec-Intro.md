## What are CrackMapExec(cme) & NetExec(nxc) 

- [CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec) (a.k.a CME) is a tool that helps assess the security of large networks composed of **Windows workstations and servers.** 

![CrackMapExec](/Introduction-to-CrackMapExec/images/cme.image.png) 

![CrackMapExec](/Introduction-to-CrackMapExec/images/nxc.image.png) 

- CME heavily uses the [Impacket](https://github.com/SecureAuthCorp/impacket) library to work with network protocols and perform a variety of post-exploitation techniques. To understand the power of CME, we need to imagine simple scenarios: 

1. We are working on an internal security assessment of over 1,000 Windows workstations and servers. How do we test whether the single set of credentials we have works for a local administrator on one or more machines? 

2. We only have one target and several sets of credentials in our possession, but we need to know if they are still valid. How do we test them quickly? 

3. We obtained local administrator credentials and want to dump the SAM file on each compromised workstation quickly. Do we use yet another tool, or do we go manually through each workstation? 


- These questions can be answered using many tools and techniques, but it can be handy to deal with multiple tools from several authors. 
- This is where [CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec) steps in and helps us automate all the little things we need during an internal penetration test. 
- CME also gathers credentials we found during the security assessment into a database so we can go back to them later as needed. 
- The output is intuitive and straightforward, and the tool works on Linux and Windows and supports socks proxy and multiple protocols. 

- Although meant to be used primarily for offensive purposes (e.g., internal pentesting), CME can be used by blue teams to assess account privileges, find possible misconfigurations, and simulate attack scenarios. 

- Since June 2021, [CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec) has been updated only on the [Porchetta](https://porchetta.industries/) platform and not on the public repository. 
- A sponsorship costs $60 for six (6) months of access to all tools on Porchetta. 
- The private repository is merged with the public repository every six (6) months. 
- However, community contributions are available to everyone immediately. 
- CrackMapExec is developed by [@byt3bl33d3r](https://twitter.com/byt3bl33d3r) and [@mpgn](https://twitter.com/mpgn_x64). 
- The official documentation can be found on the [CrackMapExec Wiki](https://wiki.porchetta.industries/). 

- On June 2023, mpgn, the lead developer of CrackMapExec, has created a new repository containing CrackMapExec version 6, the latest version of [CrackMapExec](https://github.com/mpgn/CrackMapExec), but it was later removed. 

- Some of the developers who contributed to the tool decided to create a fork to continue the project. 
- The project was renamed to **NetExec** and is at https://github.com/Pennyw0rth/NetExec. 
