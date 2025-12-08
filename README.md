# Ansible Role MariaDB
[![CI](https://github.com/supertarto/ansible-role-mariadb/actions/workflows/ci.yml/badge.svg)](https://github.com/supertarto/ansible-role-mariadb/actions/workflows/ci.yml)

Install and configure MariaDB with Ansible, on Debian. This role suits my small needs and is nt exhausitve in any way.

## Requirements
None

## Tested plateform
* Debian 12 (Bookworm)
* Debian 13 (Trixie)

## Role variables

MariaDB service name, package to install, python connector to install, port and bind address.
```yml
mariadb_daemon: mariadb

mariadb_packages:
    - mariadb-server

mariadb_python_package:
    - python3-mariadb-connector
    - python3-pymysql
mariadb_port: "3306"
mariadb_bind_address: '127.0.0.1'
```

Maria DB log files
```yml
mariadb_log: "{{ mariadb_log_path }}/mariadb.log"
mariadb_log_error: "{{ mariadb_log_path }}/mariadb.err"
```
Default character_ser and collation
```yml
mariadb_character_set_server: "utf8"
mariadb_collation_server: "utf8_general_ci"
```

System info
```yml
mariadb_config_file: /etc/my.cnf
mariadb_config_include_dir: /etc/mysql/conf.d
mariadb_socket: /run/mysqld/mysqld.sock
mariadb_pid_file: /run/mysqld/mysqld.pid
```

You can configure here the different path that are used by mariadb. The **dump_path** and **dump_path_script** are used to store the dump and the dump script. You must the backup those dump externally. The dump script is started automatically by a cron task.
```yml
mariadb_dump_path: "/var/local/dump_sql"
mariadb_dump_path_script: "/var/local/scripts"
mariadb_log_path: "/var/log/mariadb"
mariadb_datadir: /var/lib/mysql
mariadb_root_home: "/root"
```

The mariadb root user name and password. You **must** change it!
```yml
mariadb_root_username: "root"
mariadb_root_password: "CHANGE_IT_NOW"
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

If you want to use the local dump script, you must set these value, create the user in **mariadb_users**, and define the the "priv" to **\*.\*:SELECT,LOCK TABLES**
```yml
mariadb_use_dump_script: true
mariadb_dump_cron_hour: "12"
mariadb_dump_cron_minute: "22"
mariadb_dump_user: ""
mariadb_dump_pass: ""
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

## License
GPL V3.0
