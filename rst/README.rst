bkp3com
=======

Simple scripts to auto-backup configuration of the old 3com switches.

How does it work
----------------

The hardware setup consists of a **backup server** and one or more
**switches**. The backup server runs backup scripts periodically, and the
scripts download current configuration from switches. The configuration is
stored in a git repository on server. Each script commits last changes
at the end of its job.

Supported devices
-----------------

Currently supported models are:

- 4210 (3CR17334-91) - tested with ``3Com OS V3.01.12s168`` firmware,


Dependencies
------------

The following tools are required for the scripts to run

- ``sftp``,
- ``git``

Initial configuration
---------------------

#. Install dependencies
#. Create user named ``bkp3com``, with shell access and home directory
   (``/home/bkp3com`` in our examples)::

      useradd -m -c '3com auto-backup'

#. Generate SSH keys for ``bkp3com``::

      su - bkp3com
      ssh-keygen

#. Create ``.gitconfig`` for user ``bkp3com``::

      su - bkp3com
      cat > ~/.gitconfig
      [user]
        name = 3com auto-backup
        email = admin@example.com

#. Perform necessary **preparations** for your switches.
#. Write a cron job (etc. ``/etc/cron.daily/bkp3com`` to run backup scripts
   with appropriate options).

3com 4210
^^^^^^^^^

See::

    bkp3com-4210 -h


Preparations
------------

3com 4210
^^^^^^^^^

The old 4210 switches are backed up by "sftp get" method. Your backup server as
well as the switches need to be prepared for this method to work smoothly. I assume
that you already have installed ``ssh`` and ``git``, user ``bkp3com`` is
created and it has ssh keys generated. The remaining steps are following:

#. Put the generated public key to a tftp server, for example::

      # cp /home/bkp3com/.ssh/id_rsa.pub /srv/tftp/bkp3com_rsa.pub

#. Prepare your switch with the following commands (``my-server`` is the server
   where the public key is available, should be IP or FQDN)::

      tftp my-server get bkp3com_rsa.pub

      system-view
        public-key local create rsa
        NO

        public-key local create dsa
        NO

        public-key peer bkp3com import sshkey bkp3com_rsa.pub
        NO

        local-user bkp3com
          service-type ssh level 3
        quit

        ssh user bkp3com service-type sftp
        ssh user bkp3com authentication-type publickey
        ssh user bkp3com assign publickey bkp3com
        sftp server enable

      quit
      delete bkp3com_rsa.pub
      YES
      save
      YES

      quit
    
   The above commands are prepared to be copy-pasted to your switch shell (so
   they contain also ``YES/NO`` responses to some questions that switch may
   optionally ask, and if it does not ask, these responses are treated as
   errors, but they are is harmless). Don't forget to replace ``my-server``
   with your server name before pasting the code to the terminal.

#. Add the public key of the new switch to the ``.ssh/known_hosts`` of
   ``bkp3com`` user. The most straightforward method is to just connect
   to your switch via sftp (``switch-01`` is the IP or DNS name of your switch)::

      # su - bkp3com;
      # echo "quit" | sftp switch-01

   Answer ``yes`` to the question posed by ``sftp``.
