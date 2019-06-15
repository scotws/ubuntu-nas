UPS
===

This is with the UPS APC 700VA BX700U-GR

.. code:: bash

        sudo apt install apcupsd
        sudo cp /etc/apcupsd/apcupsd.conf /etc/apcupsd/apcupsd.conf.bak
        sudo vi /etc/apcupsd/apcupsd.conf

Notes some of these are just the defaults.

.. code:: bash

        ANNOY 300
        ANNOYDELAY 60
        BATTERYLEVEL 20
        DATATIME 0
        DEVICE
        EVENTSFILE /var/log/apcupsd.events
        EVENTSFILEMAX 10
        KILLDELAY 0
        LOCKFILE /var/lock
        LOGSTATS off
        MINUTES 10
        NETSERVER on
        NISIP 127.0.0.1
        NISPORT 3551
        NOLOGINDIR /etc
        NOLOGON disable
        ONBATTERYDELAY 6
        POLLTIME 90
        PWRFAILDIR /etc/apcupsd
        SCRIPTDIR /etc/apcupsd
        STATFILE /var/log/apcupsd.status
        STATTIME 0
        TIMEOUT 0
        UPSCABLE usb
        UPSCLASS standalone
        UPSMODE disable
        UPSNAME BX700U
        UPSTYPE usb

We also have to edit 

.. code:: bash

        sudo vi /etc/default/apcupsd

and there set ``ISCONFIGURED=yes``. Restart the service after configuration:

.. code:: bash

        sudo systemctl restart apcupsd.service
        sudo systemctl status apcupsd.service

The command sudo apcaccess gives a dump of features. Then to test the settings, stop the demon and run

.. code:: bash

        sudo apctest

We still have to test it by pulling the plug.

Links
-----

* http://www.apcupsd.org/manual/manual.html
* https://www.pontikis.net/blog/apc-ups-on-ubuntu-workstation)
