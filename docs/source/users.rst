Users
=====

We have four users on the system, here we'll name them `user1` to `user4`. We
already have user1 from setting up the operating system.  For NFS, we have to
make sure that the UID and GID are the same on chell and home.

.. code:: bash 

        sudo adduser user2
        sudo adduser user3
        sudo adduser user4

This gives us:

.. code:: bash

	user1   1000:1000
	user2   1001:1001
	user3   1002:1002
	user4   1003:1003

Add group home with GUID 1010 (used for pictures):

.. code:: bash

	sudo groupadd -g 1010 home

	sudo adduser user1 home 
	sudo adduser user2 home 
	sudo adduser user3 home 
	sudo adduser user4 home 

The cats do not need separate user accounts, they just use root when they feel
like it. 
