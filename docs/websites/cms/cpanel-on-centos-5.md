---
author:
  name: Linode
  email: docs@linode.com
description: 'Use cPanel to manage services on your CentOS 5 Linux VPS.'
keywords: 'cpanel,vps control panel,cpanel linux,cpanel centos'
license: '[CC BY-ND 3.0](http://creativecommons.org/licenses/by-nd/3.0/us/)'
alias: ['web-applications/control-panels/cpanel/centos-5/']
modified: Friday, February 3rd, 2012
modified_by:
  name: Brian O''Keefe
published: 'Wednesday, September 1st, 2010'
title: cPanel on CentOS 5
---

[cPanel](http://cpanel.net) is a commercial web-based control panel for server systems. It can help ease the burden of common system administration tasks such as website creation, database deployment and management, and more. This guide will help you get up and running with the [VPS Optimized cPanel](http://cpanel.net/products/cpanelwhm/vps-optimized.html) product on your CentOS 5 Linode. Please note that Linode does not sell cPanel licenses; you'll need to obtain one directly from cPanel or an authorized distributor. Additionally, Linode does not provide cPanel support, although you may contact [cPanel support](http://cpanel.net/support.html) directly once you've purchased a license. This product **must** be installed on a freshly deployed CentOS 5 VPS. These instructions should be performed as the "root" user via SSH.

DNS Prerequisites
-----------------

cPanel includes facilities for hosting your own DNS services. We generally recommend using [Linode DNS services](/docs/dns-guides/configuring-dns-with-the-linode-manager), as it provides a stable, redundant, and easily managed DNS platform. If you elect to run your own DNS services on a single Linode using cPanel, please be aware that such a setup provides no redundancy.

Should you wish to provide DNS services, you'll need to add A records for your nameservers in your WHM as described in the [DNS on cPanel guide](https://library.linode.com/web-applications/control-panels/cpanel/dns-on-cpanel#sph_nameserver-records).

If you're planning to use a domain name for your nameservers that you will also be hosting DNS service for, you'll need to ask your domain name registrar to create [DNS glue records](http://en.wikipedia.org/wiki/Domain_Name_System#Circular_dependencies_and_glue_records) based on your Linode's IP addresses before proceeding.

Basic System Configuration
--------------------------

Edit your `/etc/hosts` file to resemble the following example. Replace "hostname" with a unique name for your server, "example.com" with your domain name, and "12.34.56.78" with your Linode's public IP address. If your Linode has two IPs assigned to it, use the first IP in the list displayed on the "Remote Access" tab of the Linode Manager.

{: .file }
/etc/hosts

> 127.0.0.1 localhost.localdomain localhost 12.34.56.78 hostname.example.com hostname

Set your system's hostname by issuing the following commands, replacing quoted "hostname" entries with your system's short hostname:

    echo "HOSTNAME=hostname" >> /etc/sysconfig/network
    hostname "hostname"

Edit the `/etc/sysconfig/network-scripts/ifcfg-eth0` file to resemble the following, replacing `12.34.56.78` with your Linode's IP address and `12.34.56.1` with its default gateway. If your Linode has two IPs assigned to it, use the first IP in the list displayed on the "Remote Access" tab of the Linode Manager.

{: .file }
/etc/sysconfig/network-scripts/ifcfg-eth0
: ~~~
  DEVICE=eth0
  BOOTPROTO=none
  ONBOOT=yes
  IPADDR=12.34.56.78
  NETMASK=255.255.255.0
  GATEWAY=12.34.56.1
  ~~~

If your Linode has a second IP address, edit the `/etc/sysconfig/network-scripts/ifcfg-eth0:0` file to resemble the following. Replace `98.76.54.32` with the second IP address. No gateway should be specified for this IP address, as all traffic will be properly routed through the primary IP's gateway.

{: .file }
/etc/sysconfig/network-scripts/ifcfg-eth0:0
: ~~~
  DEVICE=eth0:0
  BOOTPROTO=none
  ONBOOT=yes
  IPADDR=98.76.54.32
  NETMASK=255.255.255.0
  ~~~

Restart networking by issuing the following command:

    service network restart

Edit the `/etc/resolv.conf` to resemble the following, replacing `11.11.11.11` and `22.22.22.22` with the DNS servers listed on the "Remote Access" tab in the Linode Manager.

{: .file }
/etc/resolv.conf
: ~~~
  nameserver 11.11.11.11 nameserver 22.22.22.22 options rotate
  ~~~
  
Make sure your package repositories and installed packages are up to date by issuing the following command:

    yum update

Your system is now ready for cPanel installation.

Install cPanel
--------------

Before proceeding, make sure you've purchased a cPanel license. You may obtain a license from the [cPanel Store](https://www2.cpanel.net/store/). Next, log into your Linode as the "root" user via SSH to its IP address (found on the "Remote Access" tab in the Linode Manager). Issue the following commands to download and install cPanel.

    cd /home
    wget -N http://httpupdate.cpanel.net/latest
    sh latest
    /usr/local/cpanel/cpkeyclt

Please note that the installation process may take a long time to complete. Once it's done, you may access cPanel at `https://12.34.56.78:2087` (replace `12.34.56.78` with your Linode's IP address). Log in with the username "root" and your root password.

Configure cPanel
----------------

Once you're logged into the cPanel control panel, you must read and accept the license agreement to continue.

[![cPanel license agreement.](/docs/assets/267-cpanel-whm-01-license-large.png)](/docs/assets/267-cpanel-whm-01-license-large.png)

Provide an appropriate contact email address. Optionally, you may enter an SMS address, AIM name, or ICQ number as well.

[![cPanel contact information entry.](/docs/assets/268-cpanel-whm-02-01-networking-contact-information.png)](/docs/assets/268-cpanel-whm-02-01-networking-contact-information.png)

Enter the fully qualified domain name (FQDN) for your server.

[![cPanel hostname/FQDN entry.](/docs/assets/269-cpanel-whm-02-02-networking-hostname.png)](/docs/assets/269-cpanel-whm-02-02-networking-hostname.png)

Appropriate DNS resolvers should be automatically filled in for you, but you may wish to check the values listed against the "Remote Access" tab in the Linode Manager.

[![cPanel DNS resolver entries.](/docs/assets/270-cpanel-whm-02-03-networking-resolvers.png)](/docs/assets/270-cpanel-whm-02-03-networking-resolvers.png)

Make sure the main network device is set to `eth0`.

[![cPanel main network device selection.](/docs/assets/271-cpanel-whm-02-04-networking-ethernet-device.png)](/docs/assets/271-cpanel-whm-02-04-networking-ethernet-device.png)

When presented with the "Setup IP Addresses" section, click "Skip This Step and Use Default Settings" to continue.

[![cPanel IP address configuration.](/docs/assets/272-cpanel-whm-03-setup-ip-addresses.png)](/docs/assets/272-cpanel-whm-03-setup-ip-addresses.png)

If you intend to use Linode's nameservers (or those provided by a third party) for authoritative DNS services, make sure you select "Disabled" in the "Name Server" column. You must list your desired nameservers in the fields provided.

[![cPanel DNS server selection using Linode nameservers.](/docs/assets/273-cpanel-whm-04-01-nameservers-linode-large.png)](/docs/assets/273-cpanel-whm-04-01-nameservers-linode-large.png)

If you wish to operate your own DNS servers on your Linode, you may select either "BIND" or "NSD" under the "Name Server" column. You must list the nameservers you set up in the "DNS Prerequisites" section of this document. We have a guide on setting up your own nameservers in WHM using a single IP address available [Here](http://library.linode.com/web-applications/control-panels/cpanel/dns-on-cpanel#sph_nameserver-records).

[![cPanel DNS server selection using custom nameservers.](/docs/assets/274-cpanel-whm-04-02-nameservers-custom-large.png)](/docs/assets/274-cpanel-whm-04-02-nameservers-custom-large.png)

We recommend against installing an FTP server on your Linode, as FTP is an outdated and insecure protocol. Instead, we recommend using [SFTP](/docs/beginners-guide/#how_do_i_upload_files_to_my_linode_) to upload and download files. However, you may install an FTP server if you wish. SFTP is available by default for any main cPanel username. If you need to add file access for multiple users, you may want to install Pure-FTPd during the configuration phase.

[![cPanel FTP server selection.](/docs/assets/275-cpanel-whm-05-ftp-large.png)](/docs/assets/275-cpanel-whm-05-ftp-large.png)

Select a mail server for your Linode. Dovecot is the recommended option.

[![cPanel mail server selection.](/docs/assets/276-cpanel-whm-06-mail-large.png)](/docs/assets/276-cpanel-whm-06-mail-large.png)

You may choose to enable or disable support for filesystem quotas. Unless you actually need to track disk usage on a per-user basis, it's best to leave this disabled.

[![cPanel quota support selection.](/docs/assets/277-cpanel-whm-07-quotas.png)](/docs/assets/277-cpanel-whm-07-quotas.png)

That's it! cPanel should now be properly configured on your Linode. For product support, please be sure to contact [cPanel support](http://cpanel.net/support.html) with any further questions you may have.

More Information
----------------

You may wish to consult the following resources for additional information on this topic. While these are provided in the hope that they will be useful, please note that we cannot vouch for the accuracy or timeliness of externally hosted materials.

- [cPanel Home Page](http://cpanel.net)
- [cPanel Support](http://cpanel.net/support.html)



