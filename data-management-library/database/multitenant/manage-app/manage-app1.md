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

   

   ##**Database Service Firewall**

   Database Service Firewall is a feature of Oracle Access Control List (ACL) since 12.2.

    Service-Level ACLs allow you to control access to specific services, including those associated with individual pluggable databases (PDBs). This functionality is part of the Database Service Firewall, which isn't specifically a multitenant feature, but it is useful for controlling access to PDBs.

   

   ![](C:\Users\vbalebai.ORADEV\github\learning-library\data-management-library\database\multitenant\manage-app\images\MT3_DB_service_firewall.png)

   

   ### SETUP Steps

   The steps include

   
   
   - Install the ACL package
   - Configure the listener
- Add the IPADDRESS to the whitelist for each PDB.
   - Verify/test.

   

   #### **Step 1.  Install ACL package**

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
   
   ```

SQL> 
   ```

   

   #### **Step 2.  Configure the listener.**

   The LOCAL\_REGISTRATION\_ADDRESS\_lsnr\_alias and FIREWALL setting must be added to the "listener.ora" file. The default listener name is LISTENER and listeners on default port 1521. However In our example the CDB1 DB is listening on listener LISTCDB1. Example setting below.
   
   ```
   ##### LOCAL_REGISTRATION_ADDRESS_lsnr_alias = ON
   #LOCAL_REGISTRATION_ADDRESS_LISTENER = ON
LOCAL_REGISTRATION_ADDRESS_LISTCDB1 = ON
   ```

   

   The `FIREWALL` attribute can be added to the listener endpoint to control the action of the database firewall.
   
- `FIREWALL=ON` : Only connections matching an ACL are considered valid. All other connections are rejected.
   - `FIREWALL=OFF` : The firewall functionality is disabled, so all connections are considered valid.

   #### Take a backup of current listener.

   You could open another termimal to take a backup.
   
   ```
```
<copy> sudo su - oracle
cd $ORACLE_HOME/network/admin
cp listener.ora listener.backup</copy>
```


   ```

  

  #### Edit Listener.ora 

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
   ```


#### **Restart lister and verify FIREWALL is ON.**

```
lsnrctl stop listcdb1
lsnrctl start listener
lsnrctl status listcdb1
```
```
[oracle@mtv30 admin]$ lsnrctl stop listcdb1

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 09-APR-2020 18:49:57

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=mtv30.sub04061927430.mtw                                                                                                             orkshop.oraclevcn.com)(PORT=1523)(FIREWALL=ON)))
The command completed successfully
[oracle@mtv30 admin]$ lsnrctl start listcdb1

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 09-APR-2020 18:50:06

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Starting /u01/app/oracle/product/19c/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 19.0.0.0.0 - Production
System parameter file is /u01/app/oracle/product/19c/dbhome_1/network/admin/list                                                                                                             ener.ora
Log messages written to /u01/app/oracle/diag/tnslsnr/mtv30/listcdb1/alert/log.xm                                                                                                             l
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=mtv30)(PORT=1523)(FIREWA                                                                                                             LL=ON)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1523)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=mtv30.sub04061927430.mtw                                                                                                             orkshop.oraclevcn.com)(PORT=1523)(FIREWALL=ON)))
STATUS of the LISTENER
------------------------
Alias                     listcdb1
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                09-APR-2020 18:50:06
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /u01/app/oracle/product/19c/dbhome_1/network/admin/lis                                                                                                             tener.ora
Listener Log File         /u01/app/oracle/diag/tnslsnr/mtv30/listcdb1/alert/log.                                                                                                             xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=mtv30)(PORT=1523)(FIREWALL=ON)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1523)))
The listener supports no services
The command completed successfully


[oracle@mtv30 admin]$ lsnrctl stat listcdb1

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 09-APR-2020 18:57:12

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=mtv30.sub04061927430.mtworkshop.oraclevcn.com)(PORT=1523)(FIREWALL=ON)))
STATUS of the LISTENER
------------------------
Alias                     listcdb1
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                09-APR-2020 18:50:06
Uptime                    0 days 0 hr. 7 min. 6 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /u01/app/oracle/product/19c/dbhome_1/network/admin/listener.ora
Listener Log File         /u01/app/oracle/diag/tnslsnr/mtv30/listcdb1/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=mtv30)(PORT=1523)(FIREWALL=ON)))
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


It can take up to 5 minutes before all services have been registered again. If you want to speed this up, login to the CDB1 using SQL*Plus and execute the command 'alter system register'.
Once all services are available, specifically the PDB1 services, we can continue with the lab.

````

 #### **Step 3.  Add the IPADDRESS to the whitelist for each PDB.**

  Each policy is represented by an access control list (ACL) containing hosts that are allowed access to a specific database service. Local listeners and server processes validate all inbound client connections against the ACL.

   Once the firewall is set and listener is resarted, We will need to register the IP address of every connection that can be accepted per PDB. We are creating a whitelist of all IP address that can connect to a service. In our multitenant environment, CDB1 and PDB1 are both services. We can add additional user defined service and add whitelist to them as well.



```
 sqlplus sys/oracle@//localhost:1523/pdb1 as sysdba

SQL*Plus: Release 19.0.0.0.0 - Production on Thu Apr 9 19:03:48 2020
Version 19.5.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.

ERROR:
ORA-12506: TNS:listener rejected connection based on service ACL filtering
```

You will see that the connection fails with error ORA-12506. Since even the local host IP address is not included in the whitelist, the listener does not allow connections.
We are clearly not allowed to access this database based on the ACL filtering available. So now we are going to add our IP address to the whitelist for the (default) service PDB1.


   SQL> show pdbs

     CON_ID CON_NAME            OPEN MODE RESTRICTED

---------- ------------------------------ ---------- ----------

   ​     2 PDB$SEED            READ ONLY NO

   ​     3 CDB4PDB1            READ WRITE NO

   ​     6 MYLOCAL            READ WRITE NO

   Step e)  Note that you will get errors if you do not include the DBSFWUSER. 

   Add the ACL service IP address for the perticulat PDB and the IP address from which to allow connections.

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



Multitenant Lockdown  

PDB Lockdown profiles prevent administrators, even if they have been granted permissions through roles, from changing certain settings or executing certain commands. In this lab we will let you work with setting up PDB Lockdown Profiles.

## Prevent changes using ALTER SYSTEM commands

The first step is to create a lockdown profile, second step is to add statements to the lockdown profile which are disabled. The profiles are created at CDB$ROOT level and can be applied to PDBs in that CDB$ROOT.

à Login to CDB1 as sysdba and create a lockdown profile

[oracle@guest lab04]$ **. oraenv**

ORACLE_SID = [CDB2] ? **CDB1**

The Oracle base remains unchanged with value /u01/oracle

 

[oracle@guest lab04]$ **sqlplus / as sysdba**

 

SQL*Plus: Release 12.2.0.1.0 Production on Tue Sep 19 13:38:12 2017

 

Copyright (c) 1982, 2016, Oracle. All rights reserved.

 

 

Connected to:

Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

 

SQL> **create lockdown profile lock01;**

 

Lockdown Profile created.

 

As a first example, we will prevent a PDB to change the settings for the parameter CURSOR_SHARING. Changing this parameter could cause changes in performance and behavior of the entire CDB. Adding a rule to the newly created LOCK01 lockdown profile is done with an ALTER LOCKDOWN PROFILE command.

à Prevent the change of CURSOR_SHARING using an ALTER SYSTEM command

SQL> **alter lockdown profile LOCK01** 

   **disable statement=('alter system') clause=('set') option=('cursor_sharing');**

 

Lockdown Profile altered.

After the lockdown profile has been created, it is not automatically active for one or more PDBs. Let us first see if we can change the parameter before we activate the lockdown profile:

à Connect to container PDB1 and display the current value of CURSOR_SHARING

SQL> **alter session set container=PDB1;**

 

Session altered.

 

SQL> **show parameter cursor_sharing**

 

NAME                 TYPE    VALUE

------------------------------------ ----------- ------------------------------

cursor_sharing            string   EXACT

à Change the value of cursor_sharing to FORCE

SQL> **alter system set cursor_sharing = FORCE;**

 

System altered.

Because the lockdown profile has not been activated for this PDB, we can still change the parameter if we have enough permissions to do so.

à Enable the lockdown profile LOCK01

SQL> **alter system set PDB_LOCKDOWN=LOCK01;**

 

System altered.

à Try to change the value of CURSOR_SHARING back to EXACT

SQL> **alter system set cursor_sharing=EXACT;**

alter system set cursor_sharing=EXACT

*

ERROR at line 1:

ORA-01031: insufficient privileges

As you can see, the lockdown profile prevents me from changing the cursor sharing. The same lockdown could also prevent changes to the SGA size, the CPU_COUNT or other parameters that could have an impact to other PDBs in the CDB.

## Prevent using Oracle Database Options

Oracle Partitioning Option is one of the options that can be disabled using PDB Lockdown profiles. In this part of the lab we will demonstrate how to disable this option for a PDB. In this example we are creating a new Lockdown Profile but this is not required. For example, both the ALTER SYSTEM lockdown and the Partitioning lockdown can co-exist inside one Lockdown Profile.

à Connect to the CDB$ROOT and create a new Lockdown Profile called LOCK02

SQL> **alter session set container=CDB$ROOT;**

 

Session altered.

 

SQL> **create lockdown profile LOCK02;**

 

Lockdown Profile created.

à Add the restriction to use Partitioning to the Lockdown Profile

SQL> **alter lockdown profile LOCK02 disable option=('Partitioning');**

 

Lockdown Profile altered.

This new lockdown profile is not active by default as well so let's try to connect to the PDB and create a small partitioned table.

à Login to the PDB1 and create a small partitioned table

SQL> **create table MyTable01 (id number) partition by hash (id);**

 

Table created.

This is working as expected because we have not enabled the Lockdown Profile LOCK02 yet. So please, enable the Lockdown Profile and test again

à Enable the lockdown Profile LOCK02 and attempt to create another partitioned table

SQL> **alter system set PDB_LOCKDOWN=LOCK02;**

 

System altered.

 

SQL> **create table MyTable02 (id number) partition by hash (id);**

create table MyTable02 (id number) partition by hash (id)

*

ERROR at line 1:

ORA-00439: feature not enabled: Partitioning

As you can see, the Lockdown Profile is doing his job like designed

```

```