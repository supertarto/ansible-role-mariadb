# Ansible Role MariaDB
[![CI](https://github.com/supertarto/ansible-mariadb/actions/workflows/ci.yml/badge.svg)](https://github.com/supertarto/ansible-mariadb/actions/workflows/ci.yml)

Install and configure MariaDB with Ansible

## Requirements
None

## Tested plateform
* Debian 10 (Buster)
* Debian 11 (Bullseye)
* Debian 12 (Bookworm)

## Role variables
Those variables let you define if you want to use a dump script, to overwrite the global my.cnf file or to update root password.

```yml
mariadb_use_dump_script: true
mariadb_overwrite_global_mycnf: true
mariadb_root_password_update: false
```

You can configure here the different path that are used by mariadb. The **dump_path** and **dump_path_script** are used to store the dump and the dump script. You must the backup those dump externally. The dump script is started automatically by a cron task.

```yml
mariadb_dump_path: "/var/local/dump_sql"
mariadb_log_path: "/var/log/mariadb"
mariadb_dump_path_script: "/var/local/scripts"
mariadb_datadir: /var/lib/mariadb
mariadb_root_home: "/root"
```

The mariadb root user name and password. You **must** change it!

```yml
mariadb_root_username: "root"
mariadb_root_password: "defaultpass"
```

Creation of databases. You can create multiples ones. The collation an encoding fields are optionnal.If not set, the value **utf8_general_ci** and **utf8** will be used.

```yml
mariadb_databases: []
# Exemple:
# - name: database1
# collation: utf8_general_ci
# encoding: utf8
```

Creations of users. The host and priv fiels are optionnal. If not set, the default value, **localhost** and **\*.\*:USAGE** will be used.

```yml
mariadb_users: []
# Exemple:
# - name: username
# host: localhost
# password: sqlpassword
# priv: "*.*:SELECT,UPDATE"
```

If you want to use the dump script, you must set these value, create the user in **mariadb_users**, and define the the "priv" to **\*.\*:SELECT,LOCK TABLES**

```yml
mariadb_dump_user: []
mariadb_dump_pass: []
```

The port and bind address used by MariaDB 

```yml
mariadb_port: "3306"
mariadb_bind_address: '127.0.0.1'
```

Definition of the logs files. Based on the **mariadb_log_path** value

```yml
mariadb_log: "{{ mariadb_log_path }}/mariadb.log"
mariadb_log_error: "{{ mariadb_log_path }}/mariadb.err"
```

Default character set and collation.

```yml
mariadb_character_set_server: "utf8"
mariadb_collation_server: "utf8_general_ci"
```

## Examples

```yml
- hosts: somehost
  roles:
    - role: supertarto.mariadb

  vars:
    mariadb_use_dump_script: true

    mariadb_databases:
      - name: database1
        collation: utf8_general_ci
        encoding: utf8

    mariadb_users: []
      - name: username
        host: localhost
        password: sqlpassword
        priv: "database1.*:SELECT,UPDATE"

      - name: "{{ mariadb_dump_user }}"
        host: localhost
        password: "{{ mariadb_dump_pass }}"
        priv: "*.*:SELECT,LOCK TABLES"   
 
    mariadb_dump_user: "dumpuser"
    mariadb_dump_pass: "Dumppassword"
```

## Installation

```bash
ansible-galaxy install supertarto.mariadb
```

## License
GPL V3.0
