SMART
=====

Installing the SMART disk drive monitoring system. This assumes email
notifications are set up correctly.

.. code:: bash 

        sudo apt install smartmontools

Edit ``/etc/default/smartmontools`` so that ``start_smartd=yes``. Now edit
``/etc/smartd.conf`` with a first test setup of

.. code:: bash 

	DEVICESCAN -m <USERNAME>@gmail.com -M test

Then restart the service with

.. code:: bash 

        sudo service smartd restart

Should send an email for every drive. Then edit the line to show

.. code:: bash 

        DEVICESCAN -a -s (S/../../1/19|L/../02/./21) -m <USERNAME>@gmail.com

Where ``-a`` is ``-H -f -t -l error -l selftest -C 197 -U 198``. This does a
short test every Monday at 19:00h and a long test every second day of the month
at 21:00h - make sure there isn't a ZFS scrub at the same time. 

.. code:: bash 

	sudo service smartd restart

And restart the service again. 

.. note:: 

        NVMe SMART support is rudimentary, see https://www.smartmontools.org/wiki/NVMe_Support.

Links
-----

* https://help.ubuntu.com/community/Smartmontools
* https://wiki.archlinux.org/index.php/S.M.A.R.T. 
* https://www.smartmontools.org/browser/trunk/smartmontools/smartd.conf.5.in
