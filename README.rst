-1 Flashing latest version
--------------------------

Just go to http://flash.getchip.com/ and follow the wizard. You will need a 
paperclip.

0 First boot
------------

Connect the chip (with no paperclip bridge), wait for the heartbeat (led), and 
then find out which tty it is.
Could be helpfull to list ``/dev/tty*`` before and after connection, to compare the 
lists. 
It usually is ``/dev/ttyACM0``.

.. code-block::

    screen /dev/ttyACM0 115200


(usr: chip, pwd: chip)

Change default password:

.. code-block::

    passwd


Once inside, check if wifi is available:

.. code-block:: 

    nmcli device wifi list


If your network is there, connect to it:

.. code-block::

    sudo nmcli device wifi connect 'your network name' password 'your wifi password' ifname wlan0


And configure avahi to use a friendly hostname instead of ips. Edit 
``/etc/avahi/services/afpd.service`` with this:


.. code-block::

    <?xml version="1.0" standalone='no'?><!--*-nxml-*-->
    <!DOCTYPE service-group SYSTEM "avahi-service.dtd">
    <service-group>
    <name replace-wildcards="yes">%h</name>
    <service>
    <type>_afpovertcp._tcp</type>
    <port>548</port>
    </service>
    </service-group>


Also edit the hostname to something better, at ``/etc/hostname`` and ``/etc/hosts``
(replace "chip" with chosen name).

And restart avahi:

.. code-block:: 

    sudo /etc/init.d/avahi-daemon restart


Copy ssh key from my local machine, and files needed on the chisa:

.. code-block::

    ssh-copy-id -o PubkeyAuthentication=no chip@chisa.local -i /home/fisa/.ssh/chisa_rsa -f
    scp /home/fisa/devel/mios/scripts/update_chisa_fisadev_com chisa:/home/chip/update_chisa_fisadev_com
    scp /home/fisa/devel/mios/scripts/backup_gmail chisa:/home/chip/backup_gmail
    scp /home/fisa/devel/mios/dotfiles/.mbsyncrc chisa:/home/chip/.mbsyncrc
    scp /home/fisa/devel/chip-install/crontab chisa:/home/chip/crontab


Update index and install packages:

.. code-block::

    sudo apt-get update
    sudo apt-get install rsync nocache git isync
    git clone https://github.com/dustinkirkland/run-one.git /home/chip/run-one
    sudo ln -s /home/chip/run-one/run-one /usr/bin/run-one


Install crontab:

.. code-block::

    crontab crontab


Configure automatic mounting of tero:

.. code-block::

    sudo mkdir -p /media/tero
    sudo chmod 544 /media/tero
    echo "/dev/sda1 /media/tero    ext4    defaults 0   0" | sudo tee -a /etc/fstab
