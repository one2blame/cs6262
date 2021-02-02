# Lesson 3

The slides for this lesson can be found [here](./pdfs/PenetrationTesting.pdf)

## Overview

This lesson discusses penetration testing and security assessments.
Penetration testing is used to evaluate the strength of an organization's
security controls to include:

* Procedural
* Operational
* Technological

The benefits of penetration testing are as follows:

* Security of the network
* Discovery of vulnerabilities
* Demonstration of threats

The scope of a penetration test can include social engineering and physical
access and the security of a network.

## Methodology

The methodology and steps of an attack are as follows:

* Footprinting
* Scanning
* Enumeration
* Gaining access
* Escalating privilege
* Pilfering
* Covering tracks
* Creating back doors

### Footprinting

* Recon and information gathering
* Find target IP address / phone number range
* Namespace acquisition / discovery
* Network topology

Techniques used for footprinting are:

* Open source research
  * Tools used are usually:
    * Google, search engines, Edgar
* Finding domain names, admins, IP addresses and name servers
  * Tools used are usually:
    * Whois (network solution; arin)
* DNS zone transfers
  * Tools used are usually:
    * Nslookup (ls -d), dig, Sam Spade

### Scanning

Determine which machines are online and their ports, services, versions,
configurations, vulnerabilities. Techniques used for scanning are:

* Ping sweeping
  * Tools used are usually:
    * Fping, icmpenum, WS_Ping, ProPack, nmap
* TCP/UDP port scanning
  * Tools used are usually:
    * Nmap, superscan, fscan
* OS detection
  * Tools used are usualy:
    * Nmap, queso, siphon

### Enumeration

More intrusive probing than just scanning, identifying valid user accounts or
poorly protected resource shares. Techniques and tools used for enumeration are:

* List user accounts
  * Tools used are usually:
    * Null sessions, DumpACL, sid2user, onSiteAdmin
* List file shares
  * Tools used are usually:
    * Showmount, NAT, legion
* Identify applications
  * Tools used are usually:
    * Banner grabbing with telnet, netcat, rpcinfo

### Gaining access

Once attackers identify a vulnerability, gaining access is done through
exploitation of said vulnerability with a tool / script, etc. Automatically
generating a working exploit from a vulnerability is still an open problem.
The techniques for gaining access is as follows:

* Password eavesdropping
  * Tools used are usually:
    * tcpdump / ssldump, l0phtcrack, readsmb
* File share brute forcing
  * Tools used are usually:
    * NAT legion
* Password file grab
  * Tools used are usually:
    * tftp, pwddump2
* Buffer overflows
  * Tools used are usually:
    * ttdb, bind, IIS, .HTR/ISM.DLL

### Escalating privilege

If only user-level access was gained from exploitation, attackers seek to gain
complete control of the system. Techniques involved with escalating privilege
are:

* Password cracking
  * Tools usually used are:
    * john the ripper, l0phtcrack
* Using known exploits
  * Some examples:
    * lc_messages, getadmin, sechole

### Pilfering

After gaining full access to a device, attackers gather info to allow access to
other trusted systems. Techniques for pilfering are:

* Elevating trust
  * rhosts, LSA secrets
* Searching for cleartext passwords
  * user data, configuration files, registry files

### Covering tracks

This step involves hiding the compromise of the system from system
administrators. Techniques involved are:

* Clearing logs
  * Useful tools are:
    * Zap, Event Log GUI
* Hiding tools
  * Useful tools are:
    * Rootkits, file streaming

### Creating back doors

Back doors will be established in various parts of the system to ensure that
privileged access is easily regained whenever the intruder needs. Techniques
involved in this step are:

* Creating accounts
* Scheduling batch / cron jobs
* Infecting startup files
* Planting remote control services
* Installing monitoring mechanisms
* Replacing applications with trojans

## Persistence and stealth

Penetration testers are more likely to be persistent and stealthy in order to
acquire as much information as possible. They'll attempt to acquire a permanent
foothold in order to establish man-in-the-midle systems, capturing credentials
and identities, and move from one host / account to another to extend control
while hiding tracks.

## Social engineering

In social engineering, users / people are the vulnerability. Social engineering
attacks evaluate the user population and attempt to identify vulnerable users,
policies, etc.

Social engineering is effective because it manipulates legitimate users into
undermining their own security system, abusing trusted relationships between
employees and the organization. This comes at no cost for the attacker -
requiring no specialized equipment or skills.

## References

1. [https://usa.kaspersky.com/resource-center/definitions/what-is-social-engineering](https://usa.kaspersky.com/resource-center/definitions/what-is-social-engineering)
2. [Securing Frame Communication in Browsers](./pdfs/post-message.pdf)