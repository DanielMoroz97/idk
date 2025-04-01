# idk
1 консоль 
daniel@HOME-PC:~$ sudo su postgres

postgres@HOME-PC:/home/daniel$ pg_lsclusters

Ver Cluster Port Status Owner     Data directory                Log file

17  main    5432 down   <unknown> /var/lib/postgresql/17/main   /var/log/postgresql/postgresql-17-main.log

17  master  5433 down   postgres  /var/lib/postgresql/17/master /var/log/postgresql/postgresql-17-master.log

postgres@HOME-PC:/home/daniel$ pg_dropcluster 17 main --stop

Warning: corrupted cluster: data directory does not exist

Warning: systemd was not informed about the removed cluster yet. Operations like "service postgresql start" might fail. To fix, run:

  sudo systemctl daemon-reload
  
postgres@HOME-PC:/home/daniel$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory                Log file

17  master  5433 down   postgres /var/lib/postgresql/17/master /var/log/postgresql/postgresql-17-master.log

postgres@HOME-PC:/home/daniel$  pg_createcluster 17 main

Creating new PostgreSQL cluster 17/main ...

/usr/lib/postgresql/17/bin/initdb -D /var/lib/postgresql/17/main --auth-local peer --auth-host scram-sha-256 --no-instructions

The files belonging to this database system will be owned by user "postgres".

This user must also own the server process.


The database cluster will be initialized with locale "C.UTF-8".

The default database encoding has accordingly been set to "UTF8".

The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/17/main ... ok

creating subdirectories ... ok

selecting dynamic shared memory implementation ... posix

selecting default "max_connections" ... 100

selecting default "shared_buffers" ... 128MB

selecting default time zone ... Europe/Moscow

creating configuration files ... ok

running bootstrap script ... ok

performing post-bootstrap initialization ... ok

syncing data to disk ... ok

Warning: systemd does not know about the new cluster yet. Operations like "service postgresql start" will not handle it. To fix, run:

  sudo systemctl daemon-reload
  
Ver Cluster Port Status Owner    Data directory              Log file

17  main    5432 down   postgres /var/lib/postgresql/17/main /var/log/postgresql/postgresql-17-main.log

postgres@HOME-PC:/home/daniel$ pg_ctlcluster 17 main start

Warning: the cluster will not be running as a systemd service. Consider using systemctl:

  sudo systemctl start postgresql@17-main
  
postgres@HOME-PC:/home/daniel$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory                Log file

17  main    5432 online postgres /var/lib/postgresql/17/main   /var/log/postgresql/postgresql-17-main.log

17  master  5433 down   postgres /var/lib/postgresql/17/master /var/log/postgresql/postgresql-17-master.log

postgres@HOME-PC:/home/daniel$ nano /etc/postgresql/17/main/postgresql.conf

postgres@HOME-PC:/home/daniel$ cd ~

postgres@HOME-PC:~$ cat >> /etc/postgresql/17/main/postgresql.conf << EOL

> listen_addresses = '172.17.171.149'
> wal_level = replica
> max_wal_senders = 10
> EOL
> 
postgres@HOME-PC:~$ nano /etc/postgresql/17/main/postgresql.conf

postgres@HOME-PC:~$ cat >> /etc/postgresql/17/main/pg_hba.conf << EOL
eplicati> host replication replicator 172.17.171.149/32 scram-sha-256
> EOL
> 
postgres@HOME-PC:~$ nano /etc/postgresql/17/main/pg_hba.conf

postgres@HOME-PC:~$ pg_ctlcluster 17 main stop

postgres@HOME-PC:~$ g_ctlcluster 17 main start

Command 'g_ctlcluster' not found, did you mean:

  command 'pg_ctlcluster' from deb postgresql-common (257build1.1)
  
Try: apt install <deb name>

postgres@HOME-PC:~$ pg_ctlcluster 17 main start


Warning: the cluster will not be running as a systemd service. Consider using systemctl:

  sudo systemctl start postgresql@17-main
  
postgres@HOME-PC:~$ wget https://storage.googleapis.com/thaibus/thai_small.tar.gz && tar -xf thai_small.tar.gz && psql < thai.sql

--2025-04-01 19:15:02--  https://storage.googleapis.com/thaibus/thai_small.tar.gz

Resolving storage.googleapis.com (storage.googleapis.com)... 173.194.73.207, 64.233.164.207, 64.233.165.207, ...
Connecting to storage.googleapis.com (storage.googleapis.com)|173.194.73.207|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 84252589 (80M) [application/x-gzip]
Saving to: ‘thai_small.tar.gz.15’

thai_small.tar.gz.15                                                            100%[====================================================================================================================================================================================================>]  80.35M  10.5MB/s    in 8.2s

2025-04-01 19:15:11 (9.76 MB/s) - ‘thai_small.tar.gz.15’ saved [84252589/84252589]

...
ALTER TABLE

postgres@HOME-PC:~$ psql -c "CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'secret$123';"

CREATE ROLE

postgres@HOME-PC:~$ psql -c "SELECT pg_create_physical_replication_slot('test');"

 pg_create_physical_replication_slot
-------------------------------------
 (test,)
 
(1 row)

![image](https://github.com/user-attachments/assets/42280b90-82a0-4b10-b7c6-e3391c4f20b1)

![image](https://github.com/user-attachments/assets/04eca8d8-2c5c-4099-ae97-91477fe1ae6a)



2 консоль 
daniel@HOME-PC:~$ sudo su postgres
[sudo] password for daniel:

postgres@HOME-PC:/home/daniel$ cat >> ~/.pgpass << EOL
> 172.17.171.149:5432:*:replicator:secret\$123
> EOL
> 
postgres@HOME-PC:/home/daniel$ chmod 0600 ~/.pgpass

postgres@HOME-PC:/home/daniel$ pg_ctlcluster 17 main stop

postgres@HOME-PC:/home/daniel$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory                Log file

17  main    5432 down   postgres /var/lib/postgresql/17/main   /var/log/postgresql/postgresql-17-main.log

17  master  5433 down   postgres /var/lib/postgresql/17/master /var/log/postgresql/postgresql-17-master.log

postgres@HOME-PC:/home/daniel$ rm -rf /var/lib/postgresql/17/main

postgres@HOME-PC:/home/daniel$ pg_basebackup -h 172.17.171.149 -p 5432 -U replicator -R -S test -D /var/lib/postgresql/17/main

pg_basebackup: error: connection to server at "172.17.171.149", port 5432 failed: Connection refused
        Is the server running on that host and accepting TCP/IP connections?

