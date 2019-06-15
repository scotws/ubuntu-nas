Firewall
========

We have the advantage that there is an actual terminal hooked up to the server,
so we can just walk over and configure stuff by hand if it doesn't work. In
other words, we can be very restrictive. The goals:

1. Everybody can access **Emby**
2. The Apple computers can access **Time Machine**
3. Everybody on the local net can access **Glances**
4. Core is the jump server for home, so chell can **ssh**; worker needs to ssh into home for backups
5. We need to allow access to Avahi (**mDNS**) to all in the local network
6. Allow **NFS** access from chell

Out of the box, we get an "inactive" with

.. code:: bash

        sudo ufw status

Which is good. First we start with the basics:


.. code:: bash

        sudo ufw default deny incoming
        sudo ufw default allow outgoing

Then we explicitly reject ssh via the normal port 22 (don't leave the user
hanging, because this will be a simple mistake that will be made often):

.. code:: bash

        sudo ufw reject ssh

For the other goals:

**Goal 1:** Emby
        .. code:: bash 

                sudo ufw allow to any port 8096 comment 'Emby HTTP'

**Goal 2:** Time Machine access from mediator
        .. code:: bash 

                sudo ufw allow from 192.168.13.22 to any port 548 comment 'TM from mediator'
                sudo ufw allow from 192.168.13.22 to any port 427 comment 'TM from mediator'

**Goal 3:** Glances from local network
        .. code:: bash 

                sudo ufw allow from 192.168.13.0/24 to any port 61208 comment 'Glances'

**Goal 4:** Allow ssh from chell and worker
        .. code:: bash 

                sudo ufw allow proto tcp from 192.168.13.20 to any port 2019 comment 'ssh from chell'
                sudo ufw allow proto tcp from 192.168.13.21 to any port 2019 comment 'ssh from worker'

**Goal 5:** Allow mDNS
        .. code:: bash 

                sudo ufw allow mdns comment 'mDNS'

**Goal 6:** Allow NFS access from chell
        This is more tricky, because the mountd changes the port by default. 

        Edit ``/etc/default/nfs-kernel-server`` and change the line

        .. code:: bash 

                RPCMOUNTDOPTS="--manage-gids"

        and make it into

        .. code:: bash 

                RPCMOUNTDOPTS="--port 33333"

        This is just a number that was easy to remember. Now restart the server.

        .. code:: bash 

                sudo service nfs-kernel-server restart

        Add the correct firewall rules:

        .. code:: bash 

                sudo ufw allow from 192.168.13.20 to any port nfs comment 'NFS from chell'
                sudo ufw allow from 192.168.13.20 to any port 111 comment 'NFS from chell'
                sudo ufw allow from 192.168.13.20 to any port 33333 comment 'NFS from chell'

        Disable and re-enable the firewall.

Status verbose gives us:

.. code:: bash 

        To           Action      From
        --           ------      ----
        5353         ALLOW IN    Anywhere          # mDNS
        2019/tcp     ALLOW IN    192.168.13.20     # ssh from chell
        2019/tcp     ALLOW IN    192.168.13.21     # ssh from worker
        8096         ALLOW IN    Anywhere          # Emby HTTP
        548          ALLOW IN    192.168.13.22     # Time Machine from mediator
        427          ALLOW IN    192.168.13.22     # Time Machine from mediator
        61208        ALLOW IN    192.168.13.0/24   # Glances
        22/tcp       REJECT IN   Anywhere          # No SSH over normal port
        2049         ALLOW IN    192.168.13.20     # NFS from chell
        111          ALLOW IN    192.168.13.20     # NFS from chell
        33333        ALLOW IN    192.168.13.20     # NFS from chell
        5353 (v6)    ALLOW IN    Anywhere (v6)     # Allow Zero Config
        8096 (v6)    ALLOW IN    Anywhere (v6)     # Emby HTTP
        22/tcp (v6)  REJECT IN   Anywhere (v6)     # No SSH over normal port


Administration stuff that might come in handy:

.. code:: bash 

        sudo ufw enable          # start the firewall
        sudo ufw status verbose  # what's going on
        sudo ufw app list        # who can pierce the firewall
        sudo iptables -L         # list of rules
        sudo ufw disable         # stop the firewall

Test with various machines to see if we can log in / do time machine / play videos.

Links
-----

* https://www.cyberciti.biz/faq/how-to-setup-a-ufw-firewall-on-ubuntu-18-04-lts-server/
* https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-18-04
* https://help.ubuntu.com/community/UFW
* https://wiki.ubuntu.com/UncomplicatedFirewall
* http://manpages.ubuntu.com/manpages/bionic/en/man8/ufw.8.html
* https://wiki.debian.org/SecuringNFS 
* https://serverfault.com/questions/377170/which-ports-do-i-need-to-open-in-the-firewall-to-use-nfs-
