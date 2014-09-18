bkp3com
=======

Simple script to auto-backup configuration of the old 3com switches.
Currently supported models are:

- 4210 (3CR17334-91) - tested with ``3Com OS V3.01.12s168`` firmware,


Dependencies
------------

The following tools are required for the scripts to run

- ``sftp``,
- ``git``

Preparations
------------

3com 4210
^^^^^^^^^

The old 4210 switches are backed up by "sftp get" method. Your backup server as
well as the switches need to be prepared for this method to work smoothly. I assume
that you already have ``openssh`` server and ``git`` installed and user
``bkp3com`` is created. The remaining steps are following:

#. Generate ssh key-pair for the ``bkp3com`` user (on backup server)::

      # su - bkp3com -c 'ssh-keygen'

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
