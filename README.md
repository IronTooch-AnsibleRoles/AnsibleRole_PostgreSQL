PostgreSQL
=========

This role is designed to deploy a single PostgreSQL database, as is typically required for applications. It makes a single database, with the name of the application. It then makes a database user, also with the application name, and grants the user rights on the application. Finally, it sets the CIDR access for the Postgres DB to allow connections to it.

Requirements
------------

None

Role Variables
--------------

- **postgres_app_name**: The name of the application that the DB is being deployed to support
  - Defaults to *exampleapp*, definitely change this
- **default_postgres_system_user**: The name of the system user that Postgres makes to administer the Db
  - Defaults to *postgres*, the standard for Debian Postgres
- **postgres_db**: The name of the database to create.
  - Defaults to *{{ postgres_app_name }}_db*
- **postgres_dbuser**: The name of the database user to create
  - Defaults to *{{ postgres_app_name }}_dbuser*
- **postgres_dbpassword**: The password to set for the DB user
  - Defaults to generating a 24 character password randomly with no special characters (I'm not confident that Postgres handles them well)
- **pga_restricted_cidr**: The CIDR address and subnet that allows connections to the Db
  - Defaults to *0/0*, which means "able to connect from any IP"
- **hba_conf_location**: The path to the HBA configuration file
  - Defaults to */etc/postgresql/{{ postgres_major_ver }}/main/pg_hba.conf*
- **postgres_pass_location**: The path to save a copy of the database password file. Set to "" to not save a copy of the file
  - Defaults to */etc/postgresql/{{ postgres_major_ver }}*

Note: Even without saving the password to a file, the raw password is available to follow-on plays with the {{ postgres_dbpassword }} variable, to be able to configure connection strings. This would be the most secure implementation.

Dependencies
------------

No role dependencies.

Example Playbooks
----------------

```yaml
# This makes a regular database, with a db named "myapp_db", a db user named "myapp_dbuser", a random password, and global network access
    - role: 'irontooch.homelab.db_postgresql'
      vars:
        postgres_app_name: "myapp"
```

```yaml
# This makes a regular database, with a db named "myapp", a db user named "myapp", sets the myapp user's password to "mypassword1234, and global network access
    - role: 'irontooch.homelab.db_postgresql'
      vars:
        postgres_app_name: "myapp"
        postgres_db: "myapp"
        postgres_dbuser: "myapp"
        postgres_dbpassword: {{ "mypassword1234" | hash('md5')}}
```

```yaml
# This makes a regular database, with a db named "myapp_db", a db user named "myapp_dbuser", sets the myapp user's password to a random value, and only allows access from the localhost (which wouldn't be weird for self-deployed apps)
    - role: 'irontooch.homelab.db_postgresql'
      vars:
        postgres_app_name: "myapp"
        postgres_pass_location: ""
        pga_restricted_cidr: "127.0.0.1/32"
```

License
-------

GPL v3.0

Author Information
------------------

Author is IronTooch
