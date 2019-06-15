Snapshots
=========

We use Sanoid https://github.com/jimsalterjrs/sanoid to automatically create and
prune ZFS snapshots. Note this is a backup server, so we don't need hourly
snapshots for the users because we're only fed their stuff once a day anyway.

Sanoid is not part of the Ubuntu distribution, so we need to set it up from the
GitHub repository.

.. code:: bash 

        sudo apt install libconfig-inifiles-perl
        cd /opt
        sudo git clone https://github.com/jimsalterjrs/sanoid
        sudo ln /opt/sanoid/sanoid /usr/sbin/
        sudo mkdir /etc/sanoid
        sudo cp /opt/sanoid/sanoid.conf /etc/sanoid/sanoid.conf
        sudo cp /opt/sanoid/sanoid.defaults.conf /etc/sanoid/sanoid.defaults.conf

In ``/etc/sanoid.conf`` we have the templates and the datasets they refer to.

.. code:: bash

        [tank/coldstore]
                use_template = archive

        [tank/h_user3]
                use_template = slacker

        [tank/h_user2]
                use_template = backup

        [tank/h_user1]
                use_template = busybee

        [tank/h_user4]
                use_template = slacker

        [tank/media]
                use_template = archive

        [tank/pictures]
                use_template = archive

        [tank/storage]
                use_template = archive

        [tank/texts]
                use_template = archive


        ### TEMPLATES ####

        # User who only occasionally logs in. We want to be able to catch when they
        # come the next day and says "dude, I lost my file"
        [template_slacker]
                frequently = 0
                hourly = 0
                daily = 7
                weekly = 4
                monthly = 4
                yearly = 1
                autosnap = yes
                autoprune = yes

        # User who does a lot of work and could lose a lot
        [template_busybee]
                frequently = 0
                hourly = 0
                daily = 31
                weekly = 0
                monthly = 12
                yearly = 1
                autosnap = yes
                autoprune = yes

        # Backup for people whose data comes from outside and is transferred by rsync.
        [template_backup]
                frequently = 0
                hourly = 0
                daily = 7
                weekly = 4
                monthly = 12
                yearly = 1
                autosnap = yes
                autoprune = yes

        # Backup for media files and other archived data. We usually don't
        # delete anything in these datasets, but only add stuff, so we want
        # to avoid cryptolocker attacks and catch "oops" accidents.
        [template_archive]
                frequently = 0
                hourly = 0
                daily = 3
                weekly = 1
                monthly = 3
                yearly = 0
                autosnap = yes
                autoprune = yes

Edit ``/etc/crontab`` by hand (we don't care about skipping one hour during
daylight savings transition):

.. code:: bash

        */5 * * * * root /usr/sbin/sanoid --cron

After about five minutes, you'll see snapshots appearing when you run ``zfs list
-t snapshot``.  

.. note:: 

        The datasets used for Time Backups are not snapshotted. 

Deleting snapshots
------------------

Use the ``-n`` switch to test what we do before we do it. Format is

.. code:: bash 

	zfs destroy -vn <FIRST-SNAPSHOT>%<LAST-SNAPSHOT> 

Where last snapshot is only the part after the ``@``. For example:

.. code:: bash 

        zfs destroy -vn tank/h_user1@auto-20190303.0900-3m%auto-20190601.0900-3m

This will also tell you how much space will be freed. Do this with without
``-vn`` to pull the trigger.

