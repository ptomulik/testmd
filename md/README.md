Contentia DDEV
==============

Preparations
------------

Clone this template

```bash
clone git@github.com/ptomulik/contentiaddev.git && cd contentiaddev/
```

Create ``.ddev/config.local.yaml`` to customize the installation for your
website, see [confid.yaml documentation](https://ddev.readthedocs.io/en/stable/users/extend/config_yaml/)

Make directory named ``web`` and put website's source code there. Trailing
slashes are crucial!

```bash
mkdir -p web
rsync -azv dev7.jmc.pl:/home/www/contentiacdn2/ web/
```

Start ddev docker containers:

```bash
ddev start
```

Create database and populate it with inital data.

```bash
ddev import-db --target-deb=dev7_web --src=./web/SQL_Structure.sql
ddev import-db --no-drop --target-db='dev7_web' --src=./web/SQL_Base_start_data.sql
```
