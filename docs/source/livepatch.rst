Livepatch
=========

Canonical Livepatch automatically updates some parts of the kernel without
rebooting. 

Log into https://ubuntu.com/livepatch and get the id number. On the machine,

.. code:: bash 

        sudo snap install canonical-livepatch
        sudo canonical-livepatch enable <NUMBER>
        canonical-livepatch status --verbose

We don't do this on chell because we reboot the thing all the time anyway.

Links
-----


* http://blog.dustinkirkland.com/2016/10/canonical-livepatch.html

