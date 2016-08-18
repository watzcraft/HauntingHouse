Part V
======

This section is dedicated to tools & applications you can or should install on your Pi. As many of them have no direct relationship to 
the home automation topic but nonetheless are so complex and powerful themselve that squeezing a description into the other chapters 
would be obfuscating the whole book, we simply pulled them out into this chapter

Radicale
--------

Overview
~~~~~~~~

*Radicale* is a lightweight CalDav & CardDav server that seems to be perfectly sized for this project. Other options may have been DaviCal, Baikal 
or even ownCloud (only to name a few), but all have a much larger footprint, bot in code and persistence requirements.

*Radicale* is a no database, no UI, no bells, no whistles implementation. We will give this
a try.

Installation
~~~~~~~~~~~~

*Radicale* should be available from the repository. You may install from
hand using the python installer, but be prepared that settings are
different.

.. code::

	sudo apt-get install radicale

I had to uncomment the line

.. code::

	ENABLE_RADICALE=yes

in /etc/default/radicale to enable automatic startup.

That's all for the first steps. Start radicale.

.. code::

	sudo service radicale

The server is now running on

.. code::

	http://<machine>:5232

Test
~~~~

Now you can simply add a calendar and some events using your favorite
CalDav client. Normally this is quite simple for example with your
smartphone CalDav client application. The default calendar location is

.. code::

	http://<machine>:5232/<user>/calendar.ics

Update
~~~~~~

If you have installed via "apt-get" (wich will give you v0.7), this may be the best moment to udpate to the latest version 1.1.1. At least for 
me the authentication part did either not work or my configuration file structure was not compatible with the version i tried.

But good news, update is simple. Just download the current tar

.. code::

	wget http://pypi.python.org/packages/source/R/Radicale/Radicale-1.1.1.tar.gz

and unpack 

.. code::

	tar xzvf Radicale-*.tar.gz
	
Switch to the unpacked directory

.. code::

	cd Radicale-1.1.1
	
and start installation

.. code::

	sudo python setup.py install


Authentication
~~~~~~~~~~~~~~

If you plan to use this in a production environment or even make the service accessible
from the outside world you really must add authentication! Radicale comes with a bunch of possibilities, the simplest
(and least secure) one being htpasswd. As my personal needs are not this advanced, i may get away with using this approach.

To activate htpasswd authentication for radicale you have to change the /etc/radicale/config file. Be warned: for my installation,
the config file template was not up to date, i had to copy the version from the radicale web site.

.. code::

	[auth]
	type = htpasswd
	
	[rights]
	type = authenticated

The other defaults are fine. Be sure to restart the radicale service after changing the config.

Now we need to add a htpasswd compatible file at /etc/radicale/users. There are some tools around that allow you to 
create such a file "by hand" - i found the easiest way to use the original apache htpasswd utility. First ensure that the apache utilities are installed.

.. code::

	sudo apt-get install apache2-utils
	
Now *htpasswd* is available. With

.. code::

	sudo htpasswd -cd /etc/radicale/users foo
	> New password:
	> Re-type new password:
	> Adding password for user foo

you create the file "/etc/radicale/users" and add the user *foo* with the password you entered at the console (omit the "c" option when the file is already present).
When you configure a client now you must use a valid user/password combination for authentication - be aware that this does not need to be the same user as you use in the calendar URL.

More security
~~~~~~~~~~~~~

Above are the most basic settings you can provide to secure your system. If you want to go the extra mile, you may add

* bcrypt support
* PAM authentication
* SSL support

Clients
~~~~~~~

Radical is not designed to have the best standard support, but most clients will do.

For my requirements i have used two options 

* CalDAV Synch for Android
   This is a great and very versatile tool that neatlessly worked with Radicale
   
* CalDav Zap
   This is a pure browser based implementation and a very nice calendar app. 
   
These two make up for a very simple remote date based control.
