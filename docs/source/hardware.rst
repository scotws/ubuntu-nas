Hardware
========

Most of the hardware was taken from the previous FreeNAS install. 


Motherboard
-----------

The main difference is the new motherboard, a `Supermicro X10SDV-4C-7TP4F
<https://www.supermicro.com/products/motherboard/Xeon/D/X10SDV-4C-7TP4F.cfm>`_
Out of the box, you can attach **20 SATA 3.0 drives** and install **one M.2 NVMe
PCIe 3.0** memory stick. This is currently not used, as are the **two SFP+ 10
GBit Ethernet** ports. They are for later expansions.

The processor is an **Intel Xeon D-1518 4/8 core 2.20 GHz**, which is overkill
for current use but should be powerful enough for later virtual machines. 


Drives
------

In this first incarnation, the **root file system** for Ubuntu 18.04 LTS is 
on a 120 GB Intel 540S SSD. Root on ZFS is still too complicated for this
project. 

.. note:: At time of writing, ZFS on Linux 0.8 was not yet integrated to the
        Ubuntu kernel, this is using 0.7.9-3ubuntu6

The **mass storage** was inherited from the FreeNAS build and consists of one
ZFS pool ("tank") constructed from two RaidZ VDEVs in 3/3/3 TB and 4/4/4 TB
configuration. The total size is 14 TB on six drives.


Extra cooling fan
-----------------

The X10SDV-4C-7TP4F is shipped without a CPU cooler, which doesn't work. Instead
of buying an extra cooler and replacing the passive heat sink, we install a
jury-rigged fan that blows air on the heat sink.


Other hardware
--------------

**RAM:** 2 x 8 GB DDR4 ECC RAM
        This is left over from a different project. As finances allow, these
        will be replaces by 4 x 16 GB ECC RAM for a total of 64 GB. Though ZFS
        loves RAM and the maximum for the board is 128 GB, this should be enough
        for our use. 

**Graphics:** VGA built-in
        In contrast to the usual setup with servers, there is an old 4:3
        monitor attached with VGA. Also, there is keyboard. Both are in the same
        room as the main computer. This means we can do stuff directly at the
        console which otherwise would require ssh.

**Case:** Fractal Design Define R5 White FD-CA-DEF-R5-WT
        Chosen for the soundproofing, provides easy storage for eight HDs and
        two SSDs. 

**UPS:** APC Back-UPS 700VA BX700U-GR

**Power:** Seasonic SSR-450RM Active PFC G-450 (450W, ATX 12V)

IPMI
----

The motherboard comes with IPMI enabled through either a separate network
interface or shared with on of the normal 1 GB Ethernet connections. We really
don't need it, but it's there and we have to secure it.

In our set, IPMI gets an address at boot via DHCP, for example something like
192.168.13.100. Write it down from the boot screen and then when the server is
powered up, use a web browser to access the IPMI interface.

The most important thing is to change the default Supermicro user name and
password from ``ADMIN/ADMIN`` to something different. 

Links
~~~~~

https://www.servethehome.com/basic-bmc-and-ipmi-management-security-practices/


BIOS
----

Make sure that **virtualization support** is enabled. Also, the **boot order**
can be tricky to get right.


LSI interface
-------------

The 16 SATA port interface card will show its own boot screen during startup. We
do not need to change anything. Note the X10SDV-4C-7TP4F only includes the
"dumb" IT mode, so we don't have to flash the BIOS to avoid hardware RAID
configurations which just get in ZFS' way.
