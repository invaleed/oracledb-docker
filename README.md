# Running Oracle DB on top of Docker Container

## Usage

Create docker volume

```bash
$ docker volume create oracle-vol
```

Run the docker image [wait until message : DATABASE IS READY TO USE!]
```bash
$ sudo docker run -d --name oracle193-ee \
-p 1521:1521 -p 5500:5500 \
-e ORACLE_SID=DBX \
-e ORACLE_PDB=PDBX \
-e ORACLE_PWD=supersecret \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-v oracle-vol:/opt/oracle/oradata \
invaleed/oracledb:19.3.0-ee


ORACLE PASSWORD FOR SYS, SYSTEM AND PDBADMIN: supersecret

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 02-APR-2020 09:27:12

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Starting /opt/oracle/product/19c/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 19.0.0.0.0 - Production
System parameter file is /opt/oracle/product/19c/dbhome_1/network/admin/listener.ora
Log messages written to /opt/oracle/diag/tnslsnr/f5315ef62df0/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=0.0.0.0)(PORT=1521)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                02-APR-2020 09:27:13
Uptime                    0 days 0 hr. 0 min. 1 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /opt/oracle/product/19c/dbhome_1/network/admin/listener.ora
Listener Log File         /opt/oracle/diag/tnslsnr/f5315ef62df0/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=0.0.0.0)(PORT=1521)))
The listener supports no services
The command completed successfully
Prepare for db operation
8% complete
Copying database files
31% complete
Creating and starting Oracle instance
32% complete
36% complete
40% complete
43% complete
46% complete
Completing Database Creation
51% complete
54% complete
Creating Pluggable Databases
58% complete
77% complete
Executing Post Configuration Actions
100% complete
Database creation complete. For details check the logfiles at:
 /opt/oracle/cfgtoollogs/dbca/DBX.
Database Information:
Global Database Name:DBX
System Identifier(SID):DBX
Look at the log file "/opt/oracle/cfgtoollogs/dbca/DBX/DBX.log" for further details.

SQL*Plus: Release 19.0.0.0.0 - Production on Thu Apr 2 09:41:13 2020
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.


Connected to:
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0

SQL> 
System altered.

SQL> 
System altered.

SQL> 
Pluggable database altered.

SQL> 
PL/SQL procedure successfully completed.

SQL> Disconnected from Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0
The Oracle base remains unchanged with value /opt/oracle
#########################
DATABASE IS READY TO USE!
#########################
The following output is now a tail of the alert.log:
PDBX(3):ALTER DATABASE DEFAULT TABLESPACE "USERS"
PDBX(3):Completed: ALTER DATABASE DEFAULT TABLESPACE "USERS"
2020-04-02T09:41:13.180317+00:00
ALTER SYSTEM SET control_files='/opt/oracle/oradata/DBX/control01.ctl' SCOPE=SPFILE;
2020-04-02T09:41:13.184665+00:00
ALTER SYSTEM SET local_listener='' SCOPE=BOTH;
   ALTER PLUGGABLE DATABASE PDBX SAVE STATE
Completed:    ALTER PLUGGABLE DATABASE PDBX SAVE STATE

XDB initialized.
2020-04-02T09:50:51.096204+00:00
PDBX(3):Resize operation completed for file# 10, old size 327680K, new size 337920K
```

Check status oracle container
```bash
$ docker ps --format "table {{.ID}}\t{{.Names}}" | grep oracle
f5315ef62df0        oracle193-ee
```

Login to container

```bash
$ docker exec -it f5315ef62df0 bash
```

Execute command sqlplus from the container.

```bash
[oracle@f5315ef62df0 ~]$ sqlplus sys/supersecret@//localhost:1521/DBX as sysdba

SQL> select name,open_mode from v$database;

NAME      OPEN_MODE
--------- --------------------
DBX       READ WRITE

SQL> select tablespace_name from dba_tablespaces;

TABLESPACE_NAME
------------------------------
SYSTEM
SYSAUX
UNDOTBS1
TEMP
USERS

SQL> select name from v$datafile;

NAME
--------------------------------------------------------------------------------
/opt/oracle/oradata/DBX/system01.dbf
/opt/oracle/oradata/DBX/sysaux01.dbf
/opt/oracle/oradata/DBX/undotbs01.dbf
/opt/oracle/oradata/DBX/pdbseed/system01.dbf
/opt/oracle/oradata/DBX/pdbseed/sysaux01.dbf
/opt/oracle/oradata/DBX/users01.dbf
/opt/oracle/oradata/DBX/pdbseed/undotbs01.dbf
/opt/oracle/oradata/DBX/PDBX/system01.dbf
/opt/oracle/oradata/DBX/PDBX/sysaux01.dbf
/opt/oracle/oradata/DBX/PDBX/undotbs01.dbf
/opt/oracle/oradata/DBX/PDBX/users01.dbf

11 rows selected.

SQL> exit
[oracle@f5315ef62df0 ~]$ exit
```

```bash
Stop Oracle container
$ docker stop -t 50 oracle193-ee
```

```bash
7. Start Oracle container
$ docker start oracle193-ee
```

