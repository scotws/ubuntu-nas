ZFS
===

As described earlier, we use ZFS because it is one of the few file systems that
can detect and correct bitrot. At time of writing, Ubuntu is the only major
Linux variant to support ZFS out of the box. 


The current setup
-----------------

.. note::

        We inherited our data storage pool "tank" from the previous FreeNAS
        install, so there is currently no discussion of how to setup and
        configure a ZFS pool and datasets.

We import the pool with ``zpool import -f tank``. Since this computer will be
used as a NAS, we don't limit the size of the ARC, which defaults to half the
RAM.

.. code::

        sudo install zfsutils

We currently have the following file systems ("datasets") for bulk storage:

.. code::

        tank/coldstore
        tank/media
        tank/pictures
        tank/storage
        tank/texts

Also, each of the users has a separate dataset for backups:

.. code::

        tank/h_user1
        tank/h_user2
        tank/h_user3
        tank/h_user4

Finally, there is a file system for the Time Machine backups:

.. code::

        tank/TM_mediator


Email notifications
-------------------

This assumes we have basic mail notifications working with postfix. 

.. code::

	sudo vi /etc/zfs/zed.d/zed.rc

so that we have (note these are commented out by default):

.. code::

        ZED_EMAIL_ADDR="<USER1'S MAIL ADDRESS>"
        ZED_EMAIL_PROG="mail"
        ZED_EMAIL_OPTS="-s '@SUBJECT@' @ADDRESS@"
        ZED_NOTIFY_VERBOSE=1
        ZED_NOTIFY_DATA=1

Follow this with

.. code::

        sudo systemctl restart zed

Test with scrub of tank, should send mail.

.. code::

        sudo zpool scrub tank

We will setup snapshots in a later step.

Links
~~~~~

* https://github.com/zfsonlinux/zfs/issues/6246


