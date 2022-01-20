Contentia DDEV
==============

Preparations

```bash
clone git@github.com/ptomulik/contentiaddev.git && cd contentiaddev/
```

Create .ddev/config.local.yaml and provide settings specific to your project,
see https://ddev.readthedocs.io/en/stable/users/extend/config_yaml/

make directory named ``web`` and put website code there.

```bash
mkdir -p web
rsync -azv dev7.jmc.pl:/home/www/contentiacdn2/ web/
```

Start development server

```bash
ddev start
ddev import-db --target-deb=dev7_web --src=./web/SQL_Structure.sql
ddev import-db --no-drop --target-db='dev7_web' --src=./web/SQL_Base_start_data.sql
```
