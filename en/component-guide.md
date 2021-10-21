## Compute > Image Builder > Installation Component Guide

## PostgreSQL 

> [Note]
> This guide is based on PostgreSQL version 13.
> If you are using a different version, please change the commands accordingly.

### How to Start/Stop PostgreSQL

```
#Start the postgresql service
shell> systemctl start postgresql-13

#Stop the postgresql service
shell> systemctl stop postgresql-13

#Restart the postgresql service
shell> systemctl restart postgresql-13
```

### Connect to PostgreSQL

After creating an instance, initially connect to PostgreSQL as follows.
<br>
```
#Log in after switching the account to postgres
shell> su - postgres
shell> psql
```

### Initial Setup After Creating PostgreSQL Instance

#### 1\. Change Port \(port\)

The provided image port is 5432, which is the PostgreSQL default port. For security reasons, it is recommended to change the port.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Enter the port to use in the postgresql.conf file.

port =port name to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-13


#Connect to the changed port as follows

shell> psql -p [changed port number]
```

#### 2\. Change the Server Log Time Zone

The default time zone logged in the server log is set to UTC. It is recommended to change it to be the same as the SYSTEM local time.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Enter the time zone to use in the postgresql.conf file.

log_timezone =time zone to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-13


#Connect to postgresql

shell> psql


#Confirm changed settings

postgres=# SHOW log_timezone;
```

#### 3\. Revoke the public Schema Privilege

By default, all users are granted CREATE and USAGE privileges for the public schema, so users who can access the database can create objects in the public schema. It is recommended to revoke the privileges to prevent all users from creating objects in the public schema.
<br>
```
#Connect to postgresql

shell> psql


#Execute the command to revoke the privilege

postgres=# REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

#### 4\. Allow Remote Access

To allow access from connections other than localhost, you need to change the listen_addresses variable and the client authentication configuration file.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Specify the addresses to allow in the postgresql.conf file.
#0.0.0.0 to allow all IPv4 addresses
#:: to allow all IPv6 addresses
#* to allow any address

listen_addresses =Addresses to allow


#Save vi editor


shell> vi /var/lib/pgsql/13/data/pg_hba.conf


#Client authentication control by IP address format
#Older client libraries do not support the scram-sha-256 method, so it needs to be changed to md5

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    Allowed DB          Allowed user         Allowed address                  scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host    Allowed DB          Allowed user         Allowed address                  scram-sha-256


#Restart the postgresql service

shell> systemctl restart postgresql-13
```

### PostgreSQL Directory Description

PostgreSQL directory and file descriptions are given below.

| Name | Description |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQL database cluster creation log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQL data file path - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL log file path - /var/lib/pgsql/{version}/data/log/\*.log |

