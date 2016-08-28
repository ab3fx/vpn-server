vpn-server
==========


Introduction
------------

This is my implementation of an internet access VPN using
[strongSwan](https://www.strongswan.org/) on Debian. It supports individual
host (i.e. single computers, including Mac OS X and iOS) and network (i.e.
multiple computers behind a router) clients.

These files were copied from a working server but have not been tested for a
fresh installation; they could be incomplete. 


Using
-----

1. Set the server up with a DNS name and a
[Let's Encrypt](https://letsencrypt.org/) certificate.

2. Install the Debian packages, e.g.
`cat apt-requirements.txt | xargs apt-get install`

3. Configure the following parameters in [ipsec.conf](etc/ipsec.conf),
[iptables](etc/iptables.save) and [ipsec.secrets](etc/ipsec.secrets).

  * HOSTNAME: The DNS hostname of the server
  * SERVERIP: The IP address of the server
  * DNSSERVERS: DNS servers to be provided to clients
  * INTERFACE: The external network interface (e.g. eth0)
  * CLIENTSCIDR: The CIDR IP address space for clients
  * NETCLIENTSCIDR: The CIDR IP address space (within CLIENTSCIDR) for network
    clients
  * HOSTCLIENTSCIDR: The CIDR IP address space (within CLIENTSCIDR) from which
    virtual IPs for individual clients will be assigned
  * USERNAME and PASSWORD: for client authentication (multiple entries
    possible)

4. Copy the configuration files to /etc (e.g. `cp -R etc /`) and change
ownership and permissions as necessary.

5. Reboot, or load kernel modules, change sysctl, insert iptables rules, and
reload configurations.

6. Ensure the server is reachable on UDP ports 500 and 4500.

7. Configure clients with local IDs of "*name*@host" for individual clients to
assigned virtual IPs and "*name*@net" for network clients which will claim the
subnets they own.


Assumptions
-----------

* Fresh iptables and strongSwan configurations

* Clients are trusted to specify which subnets will be routed to them. If this
is unsuitable, create `conn` stanzas in [ipsec.conf](etc/ipsec.conf) with
`rightsubnet` limited by `rightid` and `eap_identity`.
 
* The server is behind a firewall. If not, hardening iptables and sysctl would
 be a good idea.

