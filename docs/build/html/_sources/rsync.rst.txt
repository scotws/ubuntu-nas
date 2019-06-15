Rsync
=====

We use rsync for backups from the Linux computers to the NAS.

.. note::

        This is a rather crude way of doing things, left over from earlier
        setups. In future versions, this will probably be replaced by ``zfs
        send/receive`` or Syncoid (https://github.com/jimsalterjrs/sanoid)

In the user file ``/home/user1/rsync_home.sh`` we put:

.. code:: bash 

        #!/bin/bash
        rsync -az -e 'ssh -p 2019' --delete --exclude={.cache,.steam,.zfs,.dbus,go,snap} /home/user1/ user1@home.local:/tank/h_user1/bku_chell

We can test this with the rsync ``vn`` flags to be certain. Then, add crontab
job with ``crontab -e``:

.. code:: bash

        05 17  * * * /home/user1/rsync_home.sh

For the other users, we need to generate ssh keys, such as user2. Then, on home as that user:

.. code:: bash

        mkdir .ssh
        vi .ssh/authorized_keys

Copy the public key ``id_rsa.pub`` content to that file. To test, run 

.. code:: bash

	ssh home.local -p 2019

once from user2's account. Then, test as above (with nv) options:

.. code:: bash

        rsync -az -e 'ssh -p 2019' --delete --exclude={.cache,.steam,.zfs,.dbus,snap} /home/user2/ user2@home.local:/tank/h_user2/bku_chell

.. note:: 

        This line is the reason we do not issue a banner with sshd.

If that works, put it into a shell script like for user1 with a slightly different time:

.. code:: bash

        25 17 * * * /home/user2/rsync_home.sh

Repeat the process with other users.

Links
-----

* https://www.linode.com/docs/security/authentication/use-public-key-authentication-with-ssh/
* https://jrs-s.net/2016/09/15/zfs-snapshots-and-cold-storage/
* https://blog.fosketts.net/2016/08/18/migrating-data-zfs-send-receive/

