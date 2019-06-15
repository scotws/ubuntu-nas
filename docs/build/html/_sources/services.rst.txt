Services
========

mDNS (Avahi)
------------

Zero configuration, also known as mDNS or Avahi on Linux, is not installed by
default with Ubuntu Server. This means we can't access ``.local`` addresses.
This is our primary main way of talking to machines. We will want to do this via
the console.

.. code:: bash

	sudo apt install avahi-daemon
	sudo apt install avahi-utils

Note we will have to punch a hole in the firewall later for ``UDP 5353`` for
local machines.

To get a list of machines on the network, use

.. code:: bash

        avahi-browse -a

Links
~~~~~

* https://kb.iweb.com/hc/en-us/articles/360005117952-Guide-to-Multicast-DNS-mDNS-security-issues


Network 
-------

We want home to have a static address. In fact, our network is small and
unchanging enough we can use static addresses for a lot of things.

.. code:: bash

        sudo vi /etc/netplan/50-cloud-init.yaml

We change this to:

.. code:: yaml

        network:
            ethernets:
                eno1:
                    addresses: [192.168.13.2/24]
                    gateway4: 192.168.13.1
                    nameservers:
                      addresses: [192.168.13.8,8.8.8.8]
                    dhcp4: no
                eno2:
                    dhcp4: true
                eno7:
                    dhcp4: true
                eno8:
                    dhcp4: true
            version: 2

Despite the scary text in the file, this survives reboots every time. Here,
restart the network. From the terminal, execute

.. code:: bash

        sudo netplan apply

In the router, make sure that we get the same address every time via DHCP for
this machine. 


Links
~~~~~

* https://websiteforstudents.com/configure-static-ip-addresses-on-ubuntu-18-04-beta/
* https://www.ostechnix.com/how-to-configure-ip-address-in-ubuntu-18-04-lts/


SSH
---

Though we use the console for lots of stuff, in practice, we'll want to be able
to access the server via ssh. Remember chell is the jump server for home, other
machines should not be able to access it. 

We change a bunch of options to "harden" the server. We start by editing
``/etc/ssh/sshd_config``:

.. code::

        AllowUsers user1 
        ClientAliveCountMax 0
        ClientAliveInterval 300
        IgnoreRhosts yes
        LoginGraceTime 2m
        MaxAuthTries 5
        MaxSessions 3
        PermitEmptyPasswords no
        PermitRootLogin no
        Port 2019
        PrintMotd yes
        Protocol 2
        X11Forwarding no

Note that we change the default port (to the year this was written). We'll go
over some of these later when we take a look at the firewall. Then restart the
ssh demon.

.. code:: bash

        sudo systemctl restart ssdh

We don't want to let anybody log in with just their password, instead, we need
them to have *public keys* generated on chell. We will then copy it over from
there to home. Don't use a passphrase when prompted:

.. code:: bash

        ssh-keygen 
        cat .ssh/id_rsa.pub
        ssh-copy-id -p 2019 -i ~/.ssh/id_rsa.pub user1@home.local

On home, we then edit ``/etc/sshd_config`` to include:

.. code:: bash

	PubkeyAuthentication yes
	PasswordAuthentication no

Restart again. Because logging in with a different port and all of that gets old
fast, we create a file ``~/.ssh/config`` on home with the content:

.. code:: bash

        Host home
            Hostname home.local
            User user1
            Port 2019

Now we can just login from chell with ``ssh home`` as user1. We cannot just log
in via password. Remember chell is the jump server for home.


Links
~~~~~

* https://linux-audit.com/audit-and-harden-your-ssh-configuration/
* https://linux-audit.com/using-ssh-keys-instead-of-passwords/)
