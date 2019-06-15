NFS
===

Only operating systems from the Unix family - Linux, MacOS - will be accessing
this NAS and the environment is relatively secure, so we can use NFS instead of
Samba for better performance.

.. code:: bash

        sudo apt install nfs-kernel-server

We link the various datasets through a folder so we don't have to export the
actual datasets. This is experimental.

.. code:: bash

        mkdir /exports

        ln -s /tank/pictures/ /exports/pictures
        ln -s /tank/coldstore/ /exports/coldstore
        ln -s /tank/media/ /exports/media
        ln -s /tank/storage/ /exports/storage
        ln -s /tank/texts/ /exports/texts

In ``/etc/exports``, add the following lines:

.. note:: We leave the addresses as 192.168.0.0 instead of 192.168.13.0 as
        might be expected because we'll be putting the high-speed SFP+ 
        network on a different subnet at some point.

.. code:: bash

        /exports *(ro,fsid=0,no_subtree_check)
        /exports/pictures 192.168.0.0/255.255.0.0(rw,no_subtree_check)
        /exports/coldstore 192.168.0.0/255.255.0.0(rw,no_subtree_check)
        /exports/media 192.168.0.0/255.255.0.0(rw,no_subtree_check)
        /exports/storage 192.168.0.0/255.255.0.0(rw,no_subtree_check)
        /exports/texts 192.168.0.0/255.255.0.0(rw,no_subtree_check)

On the target machine, we need the setup in ``/etc/fstab``: 

.. code:: bash

        home.local:/exports/coldstore /mnt/coldstore  nfs noatime,noauto 0 0
        home.local:/exports/storage   /mnt/storage    nfs noatime,auto 0 0
        home.local:/exports/media     /mnt/media      nfs noatime,auto 0 0
        home.local:/exports/pictures  /mnt/pictures   nfs noatime,auto 0 0

Then run ``sudo exportfs -a`` to make sure we know about this. Finally, just to be sure, try

.. code:: bash

        sudo systemctl restart nfs-kernel-server

Links
-----

* https://unix.stackexchange.com/questions/106122/mount-nfs-access-denied-by-server-while-mounting-on-ubuntu-machines
