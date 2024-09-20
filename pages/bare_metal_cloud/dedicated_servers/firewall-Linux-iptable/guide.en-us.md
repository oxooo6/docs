---
title: Configuring the firewall on Linux with iptables
excerpt: Find out how to secure a server with iptables
updated: 2024-07-12
---

## Objective

Your dedicated server is equipped with a firewall. Firewalls create a protective barrier between trusted and untrusted networks. Firewalls work by defining rules that govern both authorized and blocked traffic between the two networks. Generally, the best practice is to accept only the traffic you require, and to completely reject everything else.

*iptables* is a Linux firewall configuration utility. This guide focuses specifically on using *iptables* to configure the firewall as part of the process of securing a dedicated server. However, you may also want to investigate a front-end utility like [Uncomplicated FireWall (UFW)](https://help.ubuntu.com/community/UFW) to further simplify the process.


> [!warning]
>
>This guide is designed to assist you in common tasks as much as possible. However, you are entirely responsible for the configuration, management, and working order of the services provided by OVHcloud. If you encounter any difficulties concerning the administration, usage, or implementation of services on a server, we recommend that you contact a [specialist service provider](/links/partner) and/or discuss the issue with [our community](https://community.ovh.com/en/).


## Requirements

- A [dedicated server](/links/bare-metal/bare-metal) in your OVHcloud account
- Administrative access (root/sudo) to your server via [SSH](/pages/bare_metal_cloud/dedicated_servers/ssh_introduction)

## Instructions

> [!primary]
>
> This guide lists the commands for an Ubuntu Server distribution.
>
> This guide is for general use. You may need to adapt some commands depending on the distribution and/or operating system you are using. Some tips may suggest using third-party tools. If you have any questions about their use, please refer to their official documentation.
>

### Step 1: Update your system

Distribution and operating system developers offer frequent software package updates, very often for security reasons. **Keeping your distribution or operating system up-to-date is essential for securing your server.**

Please refer to our guide on [securing a dedicated server](/pages/bare_metal_cloud/dedicated_servers/securing-a-dedicated-server) for more information.

### Step 2: Install the iptables firewall in Ubuntu

The *iptables* utility is used to set up tables of IPv4 packet filter rules in the Linux kernel. Each table contains built-in chains, and can also contain user-defined chains. A chain is a list of rules to match specific kinds of packets, and to outline what the system should do with those packets.


> [!primary]
>
> When you install *iptables*, *ip6tables* is also installed. The *iptables* utility filters IPv4 traffic; the *ip6tables* utility filters IPv6 traffic. The rules we cover in this Linux *iptables* tutorial concern IPv4. To configure rules for IPv6, you must use the *ip6tables* utility. These two different protocols do not work together and must be configured independently.

*iptables* is installed by default on most Linux systems. To make sure that *iptables* is installed, as follows:

```bash
sudo apt-get install iptables
```

To make your iptables rules persist across system restarts, you must also install the `iptables-persistent` package:

```bash
sudo apt-get install iptables-persistent
```

After installation, the iptables folder should contain two files for IPv4 and IPv6 rules, respectively:

- `/etc/iptables/rules.v4`
- `/etc/iptables/rules.v6`

As noted above, the latter is configured using the *ip6tables* utility not covered by this tutorial.

#### Syntax and options

Typically, an *iptables* command looks as follows:

```bash
sudo iptables [option] CHAIN_rule [-j target]
```

Here are some common *iptables* options:

* **-A, --append**: Add a rule to a chain (at the end).
* **-C, --check** *chain* *rule-specification*: Find a rule that matches the requirements of the chain.
* **-D, --delete** *chain* *rulenum*|*rule-specification*: Remove the specified rules from a chain.
* **--dport** *destination port*: Specify an IP address.
* **-F, --flush**: Delete all rules.
* **-I, --insert**: Add a rule to a chain at a given position.
* **-j, --jump** *target* : Perform this target action of the rule, i.e. what to do if the packet matches it. 
* **-L, --list**: Display all rules in a chain.
* **-m, --match** *property*: Match a specific extension module to test for a specific property, such as an IP range.
* **-N, --new-chain** *chain*: Create a new chain.
* **-P, --policy** *chain* *target*: Set the policy (ACCEPT or DROP) for the built-in (non-user-defined) chain to be the given target action.
* **-s, --source** *source IP address*: Specify a source IP address.
* **--src-range** *from[-to]*: Specify a source IP address range (with the "to" being optional).
* **-v, --verbose**: Display more info, such as packet and byte counters, when using the list option (`-vL`).
* **-X, --delete-chain** [*chain*]: Delete the supplied chain.



### Step 3: Check the current status of iptables

To display all current rules on your server, enter the following command in the terminal window:

```bash
sudo iptables -L
```

The system displays the status of your channels.<br>
The output lists three chains, respectively for traffic that's incoming, forwarded, and outgoing:

![Check-Current-iptables](images/Check-Current-iptables.PNG){.thumbnail}

### Step 4: Allow traffic on localhost

To allow traffic from your own system, *localhost*, add to the `INPUT` chain the rule accepting the localhost interface:

```bash
sudo iptables -A INPUT -i lo -j ACCEPT
```

This command configures the firewall to accept traffic for the *localhost* (`lo`) interface (`-i`). 

The result is that applications are now able to communicate with the localhost interface.

### Step 5: Allow traffic on specific ports <a name="step5"></a>

These rules allow traffic on ports you specify. Ports are numbered doorways to your system, each of them permitting specific kinds of traffic.

#### HTTP

To allow incoming HTTP traffic, accept the TCP protocol on destination port 80:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

#### HTTPS 

To allow HTTPS traffic, enter the following command:

```bash
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

#### SSH 

To allow only inbound SSH (Secure Shell) traffic, accept the TCP protocol on destination port 22:

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

> **Note**: The default SSH destination port number is 22. Change the number if your port number is different.


> [!warning]
> If you lose access to your server (e.g. by blocking the very SSH you use to connect to your server), you can always [use the KVM/IPMI tool to regain access](/pages/bare_metal_cloud/dedicated_servers/using_ipmi_on_dedicated_servers) and modify your configuration or delete your rules.
> 

### Step 6: Control traffic by IP address

Accept traffic from a specific source IP address as follows:

```bash
sudo iptables -A INPUT -s your_IP_address_to_authorize -j ACCEPT
```

> **Note**: Replace `your_IP_address_to_authorize` with the actual IP address in question.

Block traffic from a specific source IP address as follows:

```bash
sudo iptables -A INPUT -s your_IP_address_to_block -j DROP
```

> **Note**: Replace `your_IP_address_to_authorize` with the actual IP address in question.

Reject traffic that matches a source IP address range as follows:

```bash
sudo iptables -A INPUT -m iprange --src-range your_start_IP_address[-your_end_IP_address] -j REJECT
```

> **Note**: Replace `your_start_IP_address` and the optional `your_end_IP_address` with the actual IP addresses of the range in question.


### Step 7: Delete unwanted traffic

Once you allowed *only* the traffic you want, the final step is to simply add a rule to block all other incoming traffic altogether.

```bash
sudo iptables -A INPUT -j DROP
```

> [!warning]
> 
> Always perform this step last, as this is the step that blocks all except what has already been allowed. Implementing this step before [step 5 (allowing SSH)](#step5), blocks all access including the SSH access you are using to connect to your server remotely. 
>

### Step 8: Delete a rule

Delete all  as follows:

```bash
sudo iptables -P INPUT DROP 
```

A more precise method is to delete the line number of a rule.


First, list all rules:

```bash
sudo iptables -L --line-numbers
```

![line-numbers](images/Step7-all-rules.PNG){.thumbnail}

Locate the line for the firewall rule you want to remove and run this command:

```bash
sudo iptables -D INPUT <number>
```

> **Note**: Replace `<number>` with the rule line number you want to delete.


### Step 9: Save your changes

When the system is restarted, *iptables* does not keep the rules you created.
Whenever you configure *iptables* on Linux, any changes you make apply only until the next reboot.

You can save rules to Ubuntu-based systems by using one of the following methods.


#### Method 1

Log in as the root user and use the `iptables-save` method to commit your changes directly to the `rules.v4` file.

Log in as the root user:

```bash
ubuntu@server:~$ sudo su
root@server:/home/ubuntu#
```

Commit the changes: 

```bash
iptables-save > /etc/iptables/rules.v4
```

#### Method 2

Use the `netfilter-persistent` package which is installed as a dependency during the install of `iptables-persistent` install.

```bash
sudo netfilter-persistent save
```

> **Note**: The `save_rules()` method of the `netfilter-persistent` package uses `iptables-save` from **Method 1**.

Once saved, *iptables* automatically reloads the firewall rules after each reboot.

## Summary

The steps above help with configuring firewall rules on your dedicated Linux server. Feel free to experiment with these rules by deleting any rules you don't need, or flushing all the rules and starting over.

Once unauthorized traffic is propertly blocked, you can also explore the broader topic of securing your dedicated server.

## Go further

Join our [community of users](/links/community).
