Containers
==========

.. note:: 

        We currently use Docker, though Podman might make more sense for a
        system this size because it doesn't need a separate demon and doesn't
        run with root. However, Ubuntu currently has better support for Docker
        than Podman.  

You will remember we didn't install Docker during the setups of the operating
system because that would have given us the wrong version. 

.. note:: This is the easy way to install, though it doesn't give you the newest 
        version (a common problem with Ubuntu). See
        https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
        for instructions on how to install the program from the official Docker
        repository.

The basic install of Docker is with

.. code:: bash

        sudo apt install docker.io
        sudo systemctl enable docker

The second line is responsible for autostarting. What follows are the
individual container installations. We also install a container for Time Machine
backups, but we'll get to that later.


Glances
-------

Glances provides an overview of the system. Though there are monitors that
provide more information, this seems to be a good balance of size and function
for a NAS with our profile.

.. code:: bash

        sudo docker pull nicolargo/glances

Create a folder ``/root/Docker-scripts``. There, create a file ``glances-docker.sh``:

.. code:: bash

        docker run -d \
        --name glances-home \
        --restart unless-stopped \
        --publish 61208-61209:61208-61209  \
        --env GLANCES_OPT="-w" \
        --volume /var/run/docker.sock:/var/run/docker.sock:ro \
        --pid host \
        docker.io/nicolargo/glances

We start with is ``sudo ./glances-docker.sh``. To reach the interface, go to
http://home.local:61208 . 

.. note::

       This might need some tweaking, because Glances is unhappy when 70 percent
       of memory is used, which is normal for a NAS. 

Emby
----

Emby is one of three options for TV and movie streaming, the other two are Plex
and JellyFin. We create a volume for configuration files, so we don't have to
reorganize every time there is a new version. 

.. code:: bash

        sudo docker pull emby/embyserver:latest
        sudo docker volume create emby-config

Create a file in ``/root/Docker-scripts/`` named ``emby-docker.sh`` with the
content:

.. code:: bash

        docker run -d \
        --name emby-home \
        --volume emby-config:/config \
        --volume /tank/media/movies:/mnt/movies:ro \
        --volume /tank/media/series:/mnt/tv:ro \
        --publish 8096:8096 \
        --publish 8920:8920 \
        --restart unless-stopped \
        --env UID=1000 \
        --env GID=1000 \
        emby/embyserver:latest

The actual media files are kept in the ZFS datasets ``tank/media``, and we pass
them ``ro`` (read-only) to be paranoid. Run it and setup at
http://home.local:8096 . During setup, set thread count to 4 for the moment. 

Configuration backups
~~~~~~~~~~~~~~~~~~~~~

We keep a backup of the ``emby-config`` volume from
``/var/lib/docker/volumes/emby-config`` at ``/tank/storage/BKU Emby/``. The
uncompressed size (02. June 2019) is about 3 GB.


Watchtower
----------

Watchtower is a program to automatically update other containers. This seems a
bit overkill for our small collection, but we might as well set it up.

.. code:: bash

        sudo docker pull v2tec/watchtower

Create ``watchtower-docker.sh`` file in the usual folder, configured to run once
a night:

.. code:: bash

        docker run -d \
        --name watchtower-home \
        -v /var/run/docker.sock:/var/run/docker.sock \
        v2tec/watchtower --schedule "0 0 5 * * *" --cleanup

This runs the check once a day at five in the morning and gets rid of old stuff
once it has updated.


Time Machine
------------

We do this with Docker because Ubuntu (again) uses an ancient version of
netatalk.

.. code:: bash

        docker run -dit \
          --name timemachine-home
          --restart=unless-stopped
          -v /tank/TM_mediator:/timemachine
          -p 548:548
          -p 636:636
          --ulimit nofile=65536:65536
        odarriba/timemachine

We have to run another line:

.. code:: bash 

        docker exec timemachine-home add-account user1 <PASSWORD> Hive-TM /timemachine

On the Mac, use COMMAND-K to input ``afp://192.168.13.2/Hive-TM`` which is the name of
the share. This could probably be home.local as well. Mount the drive that way
by hand and then configure it as a new Time Machine backup drive.

Links
-----
  
* https://hub.docker.com/r/v2tec/watchtower/
* https://godoc.org/github.com/robfig/cron#hdr-CRON_Expression_Format
* https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
* https://github.com/nicolargo/glances 
* https://glances.readthedocs.io/en/stable/docker.html
* https://glances.readthedocs.io/en/stable/cmds.html#interactive-commands
* https://hub.docker.com/r/odarriba/timemachine/
* https://github.com/odarriba/docker-timemachine


