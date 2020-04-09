# Hands-on with Multitenant -- Network Isolation

## Lab Introduction

This is a series of hands-on labs designed to familiarize you with the  Oracle Multitenant and Network isolation feature. In these labs, We will dive into the concepts of Database Firewalls , Resource managements and Lockdown features.

### Setup

### Lab Assumptions

- Each participant has been provided a username and password to the tenancy c4u03.
- Each participant has completed the Environment Setup lab.
- Each participant has created an OCI compute instance using the database template.

There are two container databases running:

- CDB1 running on port 1523
- CDB2 running on port 1524

### Lab Setup

All the scripts for this lab are located in the /home/oracle/labs/multitenant/scripts folder.

- To access the scripts, secure shell into the OCI compute instance.

- Change to the ssh directory and ssh into your instance. The public IP address can be found by going to Compute -> Instance.

   ```
   cd /home/oracle/labs/multitenant
   ```

- Reset the container databases back to their original ports if they were changed in a previous lab. If any errors about dropping databases appear they can be ignored.

   ```
   ./resetCDB.sh
   ```




##  Database Service Firewall

Database Service Firewall is a feature of Oracle Access Control List (ACL) since 12.2.

 Service-Level ACLs allow you to control access to specific services, including those associated with individual pluggable databases (PDBs). This functionality is part of the Database Service Firewall, which isn't specifically a multitenant feature, but it is useful for controlling access to PDBs.

![](C:\Users\vbalebai.ORADEV\github\learning-library\data-management-library\database\multitenant\manage-app\images\MT3_DB_service_firewall.png)



### SETUP

   The steps include



   - Install the ACL package
   - Configure the listener
   - Add the IPADDRESS to the whitelist for each PDB.
   - Verify/test.



####    **Step 1.  Install ACL package**

   You need a package DBMS_SFW_ACL_ADMIN package. This is installed by running as sysdba. This package is owned by the DBSFWUSER schema. The procedures in this package can be run only by the DBSFWUSER user.

   ```
   <copy>sudo su - oracle

   sqlplus sys/oracle@//localhost:1523/cdb1 as sysdba  @$ORACLE_HOME/rdbms/admin/dbmsaclsrv.sql </copy>


   ```



   ```
   [opc@mtv30 ~]$ sudo su - oracle
   Last login: Mon Apr  6 21:20:38 GMT 2020 on pts/0
   [oracle@mtv30 ~]$ . oraenv
   ORACLE_SID = [CDB1] ?
   The Oracle base remains unchanged with value /u01/app/oracle
   [oracle@mtv30 ~]$ sqlplus /nolog

   SQL*Plus: Release 19.0.0.0.0 - Production on Tue Apr 7 22:35:20 2020
   Version 19.5.0.0.0

   Copyright (c) 1982, 2019, Oracle.  All rights reserved.

   SQL> conn sys/oracle@//localhost:1523/cdb1 as sysdba
   Connected.
   SQL> @$ORACLE_HOME/rdbms/admin/dbmsaclsrv.sql

   Session altered.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Grant succeeded.


   Package created.


   Session altered.

   SQL>
   ```



####    **Step 2.  Configure the listener.**

   The LOCAL_REGISTRATION_ADDRESS_lsnr_alias and FIREWALL lala setting must be added to the "listener.ora" file. The default listener name is LISTENER and listeners on default port 1521. However In our example the CDB1 DB is listening on listener LISTCDB1. Example setting below.

   ```
   # LOCAL_REGISTRATION_ADDRESS_lsnr_alias = ON
   #LOCAL_REGISTRATION_ADDRESS_LISTENER = ON
   LOCAL_REGISTRATION_ADDRESS_LISTCDB1 = ON
   ```



   The `FIREWALL` attribute can be added to the listener endpoint to control the action of the database firewall.

   - `FIREWALL=ON` : Only connections matching an ACL are considered valid. All other connections are rejected.
   - `FIREWALL=OFF` : The firewall functionality is disabled, so all connections are considered valid.

**Take a backup of current listener.**

   You could open another termimal to take a backup.

   ```
   sudo su - oracle
   cd $ORACLE_HOME/network/admin
   cp listener.ora listener.backup
   ```



**Edit Listener.ora**

  <pre>
  LISTCDB2 =
    (DESCRIPTION_LIST =
      (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = mtv28imp.sub160.vjvcn.oraclevcn.com)(PORT = 1524))
    )
  )

LISTCDB1 =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = mtv28imp.sub160.vjvcn.oraclevcn.com)(PORT = 1523) <b>(FIREWALL=ON)</b>)
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1523))
    )
  )

<b>LOCAL_REGISTRATION_ADDRESS_LISTCDB1 = ON </b>
</pre>



**Reload listener and observer  ensure you see (FIREWALL=ON) in the listerer status.**

```
lsnrctl reload listcdb1

lsnrctl status listcdb1
```

```
[oracle@mtv30 admin]$ lsnrctl reload listcdb1

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 07-APR-2020 22:51:56

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=mtv30.sub04061927430.mtworkshop.oraclevcn.com)(PORT=1523)(FIREWALL=ON)))
The command completed successfully
[oracle@mtv30 admin]$ lsnrctl stat listcdb1

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 07-APR-2020 22:52:20

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=mtv30.sub04061927430.mtworkshop.oraclevcn.com)(PORT=1523)(FIREWALL=ON)))

STATUS of the LISTENER
------------------------

Alias                     listcdb1
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                06-APR-2020 21:21:04
Uptime                    1 days 1 hr. 31 min. 15 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /u01/app/oracle/product/19c/dbhome_1/network/admin/listener.ora
Listener Log File         /u01/app/oracle/diag/tnslsnr/mtv30/listcdb1/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=mtv30)(PORT=1523)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1523)))
Services Summary...
Service "CDB1" has 1 instance(s).
  Instance "CDB1", status READY, has 1 handler(s) for this service...
Service "CDB1XDB" has 1 instance(s).
  Instance "CDB1", status READY, has 1 handler(s) for this service...
Service "a206980f93d62602e0530200000ab752" has 1 instance(s).
  Instance "CDB1", status READY, has 1 handler(s) for this service...
Service "pdb1" has 1 instance(s).
  Instance "CDB1", status READY, has 1 handler(s) for this service...
The command completed successfully
```





   Each policy is represented by an access control list (ACL) containing hosts that are allowed access to a specific database service. Local listeners and server processes validate all inbound client connections against the ACL.

   Once the firewall is set and listener is resarted, We will need to register the IP address of every connection can be accepted per PDB. We are creating a whitelist of all IP address that can connect to it.

   Below are the steps to configure the DB for PDB isolation using  Database service firewall.

   Step a)  You need a package DBMS_SFW_ACL_ADMIN package. This is installed by running as sysdba. This package is owned by the DBSFWUSER schema. The procedures in this package can be run only by the DBSFWUSER user.

   SQL> conn / as sysdba

   Connected.

   -- Create user and package.

   SQL> @$ORACLE_HOME/rdbms/admin/dbmsaclsrv.sql

   Step B)

   Configure the listener.ora and set LOCAL_REGISTRATION_ADDRESS_LISTENER and firewall=on. Below is the sample listener.ora with modification in red.

   LISTENER =

    (DESCRIPTION_LIST =
    
     (DESCRIPTION =
    
      (ADDRESS = (PROTOCOL = TCP)(HOST = CDB4.compute-dbuser19.oraclecloud.internal)(PORT = 1521) (firewall=on))
    
     )
    
    )

   VALID_NODE_CHECKING_REGISTRATION_LISTENER=ON

   SSL_VERSION = 1.0

   LOCAL_REGISTRATION_ADDRESS_LISTENER= (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))

   Step C)  Start the listener.

   [oracle@CDB4 admin]$ lsnrctl start

   LSNRCTL for Linux: Version 12.2.0.1.0 - Production on 10-MAR-2017 22:35:08

   Copyright (c) 1991, 2016, Oracle. All rights reserved.

   Starting /u01/app/oracle/product/12.2.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 12.2.0.1.0 - Production

   System parameter file is /u01/app/oracle/product/12.2.0/dbhome_1/network/admin/listener.ora

   Log messages written to /u01/app/oracle/diag/tnslsnr/CDB4/listener/alert/log.xml

   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=CDB4.compute-dbuser19.oraclecloud.internal)(PORT=1521)(FIREWALL=ON)))

   Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=CDB4.compute-dbuser19.oraclecloud.internal)(PORT=1521)(firewall=on)))

   STATUS of the LISTENER

   \------------------------

   Alias           LISTENER

   Version          TNSLSNR for Linux: Version 12.2.0.1.0 - Production

   Start Date        10-MAR-2017 22:35:08

   Uptime          0 days 0 hr. 0 min. 0 sec

   Trace Level        off

   Security         ON: Local OS Authentication

   SNMP           OFF

   Listener Parameter File  /u01/app/oracle/product/12.2.0/dbhome_1/network/admin/listener.ora

   Listener Log File     /u01/app/oracle/diag/tnslsnr/CDB4/listener/alert/log.xml

   Listening Endpoints Summary...

    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=CDB4.compute-dbuser19.oraclecloud.internal)(PORT=1521)(FIREWALL=ON)))

   The listener supports no services

   The command completed successfully

   [oracle@CDB4 admin]$ sqlplus / as sysdba

   SQL*Plus: Release 12.2.0.1.0 Production on Fri Mar 10 22:36:55 2017

   Copyright (c) 1982, 2016, Oracle. All rights reserved.

   Step D) Now setup the local_listener  to point to the listener.

   SQL> alter system set local_listener='(ADDRESS = (PROTOCOL=TCP)(HOST=localhost)(PORT=1521))';

   System altered.

   SQL> alter system register;

   System altered.

   SQL> show pdbs

     CON_ID CON_NAME            OPEN MODE RESTRICTED

---------- ------------------------------ ---------- ----------

   ​     2 PDB$SEED            READ ONLY NO

   ​     3 CDB4PDB1            READ WRITE NO

   ​     6 MYLOCAL            READ WRITE NO

   Step e)  Note that you will get errors if you do not include the DBSFWUSER.

   Add the ACL service ipaddress for the perticulat PDB and the IP address from which to allow connections.

   SQL> execute  dbms_sfw_acl_admin.ip_add_pdb_ace('CDB4PDB1','140.86.12.219');

   BEGIN dbms_sfw_acl_admin.ip_add_pdb_ace('CDB4PDB1','140.86.12.219'); END;

      *

   ERROR at line 1:

   ORA-06550: line 1, column 7:

   PLS-00201: identifier 'DBMS_SFW_ACL_ADMIN.IP_ADD_PDB_ACE' must be declared

   ORA-06550: line 1, column 7:

   PL/SQL: Statement ignored

   SQL> exec DBSFWUSER.DBMS_SFW_ACL_ADMIn.ip_add_pdb_ace('CDB4PDB1','140.86.12.219');

   PL/SQL procedure successfully completed.

   SQL> exec DBSFWUSER.dbms_sfw_acl_admin.commit_acl;

   PL/SQL procedure successfully completed.

   SQL> exec DBSFWUSER.DBMS_SFW_ACL_ADMIn.ip_add_pdb_ace('CDB4PDB1','140.86.39.63');

   PL/SQL procedure successfully completed.

   SQL> exec DBSFWUSER.dbms_sfw_acl_admin.commit_acl;

   PL/SQL procedure successfully completed.

   SQL> alter system register;

   System altered.

   SQL> exec DBSFWUSER.DBMS_SFW_ACL_ADMIn.IP_REMOVE_PDB_ACE('CDB4PDB1','140.86.39.63');

   PL/SQL procedure successfully completed.

   SQL> alter system register;

   System altered.

   Step f) Connect from remote client and test. You will either get connected or  access denied error depending on the ACL rule.

   select * from DBSFWUSER .ip_acl;

   SERVICE_NAME                 HOST

-------------------------------------------- --------------------------------

   "48ADC37AB9052039E0536E99C40AD0FD"      140.86.12.219

   "48ADC37AB9052039E0536E99C40AD0FD"      140.86.39.63

   "CDB4PDB1"                  140.86.12.219

   "CDB4PDB1"                  140.86.39.63

   SQL> conn system/Saturn_02@//140.86.39.27/CDB4PDB1

   Connected.

   if the sqlplus is run from a client ip not in ACL, you the the following error.

   SQL> conn system/Saturn_02@//140.86.39.27/CDB4PDB1

   ERROR:

   ORA-46981: Access to service CDB4PDB1 from 140.86.39.63 was denied.
