Mail
====

We need a basic mail setup to send notifications from ZFS and other systems in
case of an error. We use Postfix.

.. code:: bash 

        sudo apt install postfix

During setup, configure for an "Internet Site". We use ``home.local`` as the
sending address and ``smtp.gmail.com:587`` as the relay host. Go to
https://myaccount.google.com/apppasswords to get a password for "chell mail" and
create the file ``/etc/postfix/sasl/sasl_passwd`` with the content:

.. code:: bash

        [smtp.gmail.com]:587 <USERNAME>@gmail.com:<PASSWORD>

This needs to be added to a data bank:

.. code:: bash

        sudo postmap /etc/postfix/sasl/sasl_passwd

In ``/etc/postfix/main.cf``  set the line 

.. code:: bash

        relayhost = [smtp.gmail.com]:587

Shorten the line with the banner to 

.. code:: bash

        smtpd_banner = $myhostname ESMTP 

for security reasons. And add the bunch of lines

.. code:: bash

        # Enable SASL authentication
        smtp_sasl_auth_enable = yes
        # Disallow methods that allow anonymous authentication
        smtp_sasl_security_options = noanonymous
        # Location of sasl_passwd
        smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
        # Enable STARTTLS encryption
        smtp_tls_security_level = encrypt
        # Location of CA certificates
        smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

Edit aliases file with ``sudo vi /etc/aliases`` so it reads:

.. code:: bash

        postmaster: root
        admin: root
        ubuntu: root
        root: <USERNAME>@gmail.com

The run sudo newaliases for the databank file and sudo systemctl restart postfix. Test with a mail that ends with a dot:

.. code:: bash

        sendmail <USERNAME>@gmail.com
        From: <USERNAME>@<OTHER_ADDRESS>
        Subject: Test mail
        This is a test email

This is basic mail setup. We need the mailutils package for further use.

.. code:: bash

	sudo apt install mailutils

.. note:: This setup will now allow us to send all kinds of mail with

        .. code::

                mail -s "<SUBJECT>" the.address@the.address
                <MORE TEXT>
                <CTRL>-d


Links
-----

* https://www.linode.com/docs/email/postfix/configure-postfix-to-send-mail-using-gmail-and-google-apps-on-debian-or-ubuntu/


