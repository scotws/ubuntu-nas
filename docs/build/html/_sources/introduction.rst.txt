Introduction
============

In 2019, the motherboard of my FreeNAS server died. Instead of simply replacing
it and continuing, I decided to switch to a self-created NAS based on Ubuntu
Server 18.04 LTS with ZFS and containers (Docker). This document is based on the
line-by line notes I made, and provided for anybody who is thinking about
building such a system themselves.

However, **you probably don't want to do this**. I'm no server expert, this
machine is set up for a very special use case and threat scenario, and this text
doesn't even attempt to provide a complete guide. One reason I wrote it, in
fact, is to learn reStructured text, Sphinx, and Read the Docs for other
projects. 

Still, if you feel you must, by all means read on.

.. warning:: Follow the instructions and suggestions in this text at your own
        risk. There is a very real chance that you could lose data, even all of
        it. I take no responsibility for anything bad or even mildly irritating 
        that happens.

If you need more hand-holding:
        **FreeNAS** (http://freenas.org) comes with extensive documentation and a
        very helpful community. It served me well for years, and if I hadn't
        being trying to learn more about servers and containers, I would have stayed
        with it. FreeNAS comes with the ZFS file system as well.

If you need *lots* more hand-holding:
        There are a bunch of **commercial NAS** vendors, for example Synology or
        QNAP. Usually, you just need to add the drive and the setup does the
        rest; however, they rarely come with ZFS. 

If you need more power and features:
        **Ansible-NAS** (https://github.com/davestephens/ansible-nas) is a far more
        ambitious variant of a Ubuntu NAS by Dave Stephens. As the name says, it
        makes extensive use of Ansible for configuration, and comes with
        a higher level of configuration. Full disclosure: I have contributed to the
        documentation.

