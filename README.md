# Cisco Switch SSH Configuration Lab

A beginner-friendly networking lab built in **Cisco Packet Tracer** to
practise configuring secure remote management on a Cisco 2960 switch.

## Project Overview

This lab connects one PC to a Cisco 2960 switch and configures a
management IP address, local user authentication, SSH access, and
privileged EXEC access. The connection is tested from the PC's **Desktop
→ SSH Client**.

## Objectives

-   Build a small PC-to-switch LAN.
-   Configure a management IP address on VLAN 1.
-   Create a local user for SSH authentication.
-   Generate RSA keys and enable SSH version 2 where supported.
-   Restrict VTY remote access to SSH.
-   Verify connectivity and privilege levels.
-   Save the switch configuration.

## Topology

``` text
                 Console cable
              +-------------------+
              |                   |
           +------+           +---------+
           | PC0  |-----------| Switch1 |
           +------+ Ethernet  +---------+
                              Cisco 2960
```

The PC has an Ethernet connection to switch port `FastEthernet0/1`. A
separate console connection was used for initial switch configuration.
The PC's SSH Client was then used for remote access.

## Addressing

  -----------------------------------------------------------------------
  Device /          IP address        Subnet mask       Purpose
  Interface                                             
  ----------------- ----------------- ----------------- -----------------
  PC0               `192.168.1.10`    `255.255.255.0`   Lab workstation

  Switch VLAN 1     `192.168.1.2`     `255.255.255.0`   Switch management
  (SVI)                                                 
  -----------------------------------------------------------------------

No default gateway is required for this same-subnet lab.

## Configuration Summary

The switch was configured with the following settings. These are
representative commands used in the lab; exact command support and
output may vary by Packet Tracer version.

### 1. Set the hostname and domain

``` text
hostname office-switch
ip domain-name lab.local
```

### 2. Create a local SSH user

``` text
username admin secret <USE-A-STRONG-LAB-PASSWORD>
```

Replace the placeholder with a password when configuring your own lab.
Do **not** publish real passwords or secrets in a public repository.

### 3. Configure the management SVI

``` text
interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
```

### 4. Generate RSA keys and enable SSH

``` text
crypto key generate rsa
ip ssh version 2
```

For this lab, a 1024-bit RSA key was generated after the switch
indicated that SSH version 2 required a key of at least 768 bits. The
simulated device reported SSH version `1.99`; Packet Tracer's simulated
output may differ from real hardware.

### 5. Allow SSH on VTY lines

``` text
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
```

`login local` tells the switch to authenticate with its local username
database. `transport input ssh` allows SSH and disallows Telnet on those
VTY lines.

### 6. Configure privileged access

``` text
enable secret <USE-A-STRONG-ENABLE-SECRET>
```

An enable secret is used when moving from user EXEC mode
(`office-switch>`) to privileged EXEC mode (`office-switch#`). The
secret is intentionally not included in this README.

### 7. Save the configuration

``` text
copy running-config startup-config
```

This copies the active configuration to the startup configuration so it
is retained after a restart.

## Verification and Results

The lab produced these results:

-   The PC successfully pinged the switch management address
    `192.168.1.2`.
-   `show ip interface brief` displayed VLAN 1 as `up/up` with IP
    address `192.168.1.2`.
-   The SSH Client reached the `office-switch>` prompt.
-   After entering `enable` and supplying the configured enable secret,
    the prompt changed to `office-switch#`.
-   `show privilege` reported `Current privilege level is 15`.
-   `show running-config` confirmed that both VTY ranges used
    `login local` and `transport input ssh`.
-   The configuration was saved with
    `copy running-config startup-config`.

An initial SSH authentication attempt failed before a later login
succeeded. This was useful practice in distinguishing authentication
problems from IP connectivity problems.

## Useful Verification Commands

  Command                                Purpose
  -------------------------------------- -----------------------------------------
  `show ip interface brief`              Check interface IP addresses and status
  `show ip ssh`                          Check SSH status and simulated version
  `show running-config`                  Inspect active configuration
  `show privilege`                       Display current privilege level
  `ping 192.168.1.2`                     Test PC-to-switch IP connectivity
  `copy running-config startup-config`   Save the configuration

## Skills Practised

-   Basic LAN connectivity
-   Cisco IOS CLI navigation
-   Switch management using an SVI
-   Local user authentication
-   SSH and RSA key configuration
-   VTY line access control
-   Privilege levels and enable secrets
-   Configuration verification and saving
-   Basic troubleshooting using ping and show commands

## Repository Structure

``` text
cisco-switch-ssh-lab/
├── README.md
├── screenshots/
│   ├── network-topology.png
│   ├── switch-configuration.png
│   └── ssh-privilege-level-15.png
└── configs/
    └── sanitized-running-config.txt
```

Add your own screenshots to `screenshots/` and a sanitized configuration
to `configs/`. Only use the filenames above if you actually add those
files.

## Security Notes

-   This is a learning lab, not a production configuration.
-   Do not commit passwords, private keys, or unredacted configuration
    containing secrets.
-   Use unique, strong credentials on real devices.
-   In a real company, administrative access should be limited to
    authorized users and follow the organization's access-control and
    change-management policies.
-   Cisco Packet Tracer is a simulator, so its commands and SSH
    behaviour may not exactly match physical Cisco hardware.

## Next Steps

-   Add a second PC and test communication through the same switch.
-   Practise assigning IP addresses and troubleshooting failed pings.
-   Build VLANs and test communication between devices in different
    VLANs.
-   Learn how a router or Layer 3 switch enables communication between
    subnets.

------------------------------------------------------------------------

**Status:** Completed beginner lab --- SSH remote login and privilege
level 15 verified in Cisco Packet Tracer.
