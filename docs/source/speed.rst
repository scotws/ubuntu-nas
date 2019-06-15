Speed
=====

These are tests performed with a 1 GBit Ethernet connection. To test the
connection speed with iperf, install it first on both machines, chell and home.

.. code:: bash

	sudo apt install iperf3

On home, start the server (``-f M`` gives format in MB/sec)

.. code:: bash

        iperf3 -s -f M

Now iperf will tell us which port it is listening at. We have to open the
firewall for testing:

.. code:: bash

	sudo ufw allow 5201

On chell, start the client with :

.. code:: bash

        iperf3 -f M -c home.local

This gives us **112 MB/sec** for memory-to-memory transfers, as expected over a
1 GBit Ethernet connection. Disk to memory test gives us the same thing, with
this on chell (the sender):

.. code:: bash

	iperf3 -f M -c home.local -i1 -t 40

For memory-to-disk, we start the server on home (with CWD as ``/home/user1`` on an SSD)

.. code:: bash

        iperf3 -s -f M -F test

the receiving end. This gives us about **13.4 MB/sec** from memory to a SSD. We can
also test with storing the data on one of the ZFS pool datasets (currently 2 x
RaidZ three-disk VDEV) :

.. code:: bash

        iperf3 -s -f M -F /tank/h_user1/test

This gives us **9.7 MB/sec** from memory to HD RaidZ.

On home, remember to close the firewall again and to delete the test files,

.. code:: bash

        sudo ufw deny 5201
        rm test
        rm /tank/h_user1/test

Summary
-------

+-------------------+---------+-------------+
| Type              | Network |  Speed      | 
+===================+=========+=============+
| mem-to-mem        |  1 GBit | 112 MB/sec  |
+-------------------+---------+-------------+
| mem-to-ssd (ext4) |  1 GBit | 13.4 MB/sec |
+-------------------+---------+-------------+
| mem-to-hd (ZFS)   |  1 GBit | 9.7 MB/sec  |
+-------------------+---------+-------------+


Links
-----

* https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/disk-testing-using-iperf/
