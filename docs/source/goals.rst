Goals 
=====

This introduces the basic setup in the house where the NAS will be installed,
the other machines on the network, and some of the names and addresses used.

.. note:: Some of this information differs from the actual setup for security
        reasons. It is possible that this has created inconsistencies at some
        points when I didn't pay attention. Please report these as errors.


House overview
--------------

The NAS is part of a computer setup in a single-family house with four human
users and two cats. The local network is not accessible from the outside.
Internet connection is through a DSL modem, which also (currently) serves as the
DHCP server. The local DNS connection goes through PiHole (https://pi-hole.net/)
on a Raspberry Pi.

There are also Windows computers in the house, mainly for gaming, but they are
considered **untrusted** by default and may not access the NAS. 

Various people work at various strange times in the house, so the Raspberry Pi
and the NAS itself are on all the time. Also, cats have weird sleep schedules.


Network
-------

To keep things simple, all machines will be considered to be part of the IPv4
local network 192.168.13.0/24. For the purpose of this document, the server's
name will be **home** and the main computer we work from **chell**, after the
character in the Valve computer game. Most machines are assigned *static
addresses*. In particular: 

+----------+---------------+---------+----------------+
| home     | 192.168.13.2  | NAS     | Ubuntu Server  |
+----------+---------------+---------+----------------+
| chell    | 192.168.13.20 | user PC | Ubuntu Desktop |
+----------+---------------+---------+----------------+
| worker   | 192.168.13.21 | user PC | Ubuntu Desktop |
+----------+---------------+---------+----------------+
| mediator | 192.168.13.22 | laptop  | MacOS          |
+----------+---------------+---------+----------------+

There are also various other machines such as mobile phones, Chromebooks and
iPads that connect to the network but are not relevant here.

In practice, most user addressing uses mDNS (zero configuration) internally. As
we will see later, this means we access the NAS with commands such as

.. code:: bash

        ssh home.local

This has the pleasant side effect of automatically locking out the Windows
machines, which do not support zero configuration out of the box. 


NAS duties
----------

The NAS in this house is expected to provide the following services, in order of
importance:

* Storage for documents and media, especially family photos
* Auto backup for the users' data from Linux and MacOS machines (not: Windows)
* Low-traffic Emby server (usually only one user)
* Serve NFS shares to one other Ubuntu machine chell
* Allow experimenting with containers and virtual machines

In the future, other functions might be added such as game server or a family
chat bot. 

The storage function has the highest priority, especially for family photos,
which are considered irreplaceable. As such, they must be protected from bitrot
by a *self-healing file system*. In practice, this means either ZFS or Btrfs. ZFS
has been in production longer, and Btrfs is far less battle-tested and comes
with various warnings about how things are (still) not production ready.
Therefore, ZFS is the main mass storage file system for the NAS.

.. important:: At the most basic level, this NAS is a life-support system for
        ZFS.

The other functions are secondary. 


Threat assessment
-----------------

The NAS is operating in a low-threat environment. It is not accessible from the
internet, there is especially no VPN or other (known) way to reach it from
outside. The machine itself is physically secured in the sense that if bad
people are in my house, the threat to the NAS will not be my first concern.
Still, the setup will follow *best practices* as far as possible with hardening
and firewall. 

The main threat here is **data loss** through hard drive failure, power outage,
cat intervention, or other such factors. The main strategy here is *multiple
redundant backups*, some of them off-site. 

The secondary threat is **user screw-ups**, especially accidentally deleting
data. 


NAS overview
------------

Though the NAS can be accessed directly by the console, mostly it will be
administered from the computer chell. This functions as a **jump server** - other
machines will be barred from accessing the NAS as much as possible. Since chell
is powered down when not in use, this means that accessing the NAS through the
network using ssh is usually not possible at all. 

