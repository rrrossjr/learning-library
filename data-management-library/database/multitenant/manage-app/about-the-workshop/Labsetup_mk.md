#  Multitenant Workshop setup

## OCI cloud account

This Lab uses Linux server with Oracle Software from Oracle Marketplace. We can get to this image by logging into Oracle Cloud.  The Lab is self driven and can the done in the following ways.

- Using Always Free account
- Using Your Oracle Cloud account


#### Always Free Account
If you are using the always free account, the free account gives access to Autonomous database and one OCPU VM server. For more information check out the Link on how to get it.

#### Oracle Cloud Account
If you already have Cloud Account, then you will already have tenant name, username and password needed to login and provision a workshop server.


------------------------------------------------------------------------



## Generate an SSH Key Pair

If you already have an ssh key pair, you may use that to connect to your environment. Based on your laptop config, choose the appropriate step to connect to your instance.
For for information check out the **[link](https://github.com/oracle/learning-library/blob/master/common/labs/generate-ssh-key/generate-ssh-keys.md)**.
 The link also describes how to connect to Linux server once its provisioned. We will need to revist this once we have provisioned the SSH keys.




------



## Provision VM with19c Database software from Oracle Marketplace

From a browser go to https://cloudmarketplace.oracle.com/marketplace/oci

and in the Application search box, enter database and click **Go**.

<img src="F:\work\PTS\Multitenant_work\01Search.png" style="zoom:80%;" />



------

Then click on the Oracle **Database (Single Instance) **

<img src="F:\work\PTS\Multitenant_work\02SearchResult.png" style="zoom:80%;" />





Click on <img src="F:\work\PTS\Multitenant_work\03getApp.png" style="zoom:80%;" />

------

Pick Region and Click **Sign In**

<img src="F:\work\PTS\Multitenant_work\04PickRegion.png" style="zoom:80%;" />

Now fill the **TENANT NAME, USERNAME and PASSWORD and Click  Sign In**



Choose **19c Database** and Compartment and Region that was provided by the instructor and click Launch Instance

![](F:\work\PTS\Multitenant_work\05Choose19c .png)





Enter Your Instance Name .

If you do not see the Oracle Database (single Instance)  in the image, refresh the browser

![](F:\work\PTS\Multitenant_work\06InstanceName.png)



Choose

###### Availability Domain Based on your Last Name

###### Virtual Machine for  Instance Type

###### VM.Standard2.1 (Virtual Machine)

###### Public Subnet  for Subnet

###### Assign a public IP address



<img src="F:\work\PTS\Multitenant_work\07PublicIP.png" style="zoom:80%;" />

------

If you do not have a VCN you can create one now and give it a name.

ie:

![](F:\work\PTS\Multitenant_work\Create VCN Simple.PNG)

You do not have any virtual cloud networks in this compartment.

New virtual cloud network

System will automatically create a new virtual cloud network in this compartment with access to the internet. You can set up firewall rules and security lists to control ingress and egress traffic to your instances.



Add SSH keys .  You could use the ssh keys generated.

![](F:\work\PTS\Multitenant_work\08sshkey.png)



###### Create the Instance

![](F:\work\PTS\Multitenant_work\09Create.png)



------



## Connect to your instance

Based on your laptop config, choose the appropriate step to connect to your instance.

NOTE: You cannot connect while on VPN or in the Oracle office on clear-corporate (choose clear-internet). Also, the ssh-daemon is disable for the first 5 minutes or so while the instance is processing. If you are unable to connect and sure you have a valid key, wait a few minutes and try again.

### Connecting via MAC or Windows CYGWIN Emulator

1. Go to Compute -> Instance and select the instance you created (make sure you choose the correct compartment)

2. On the instance homepage, find the Public IP addresss for your instance.

3. Open up a terminal (MAC) or cygwin emulator as the opc user. Enter yes when prompted.

   ```
   ssh -i ~/.ssh/optionskey opc@<Your Compute Instance Public IP Address>
   ```

   ![img](https://oracle.github.io/learning-library/data-management-library/database/options/img/ssh-first-time.png)

4. Continue to [Section 5b-Run the Setup Scripts](https://oracle.github.io/learning-library/data-management-library/database/options/multitenant-selfservice.html#section-5b-run-the-setup-scripts)

### Connecting via Windows

1. Open up putty and create a new connection.

   ```
   ssh -i ~/.ssh/optionskey opc@<Your Compute Instance Public IP Address>
   ```

   ![img](https://oracle.github.io/learning-library/data-management-library/database/options/img/ssh-first-time.png)

2. Enter a name for the session and click **Save**.

   ![img](https://oracle.github.io/learning-library/data-management-library/database/options/img/putty-setup.png)

3. Click **Connection** > **Data** in the left navigation pane and set the Auto-login username to **opc**.

4. Click **Connection** > **SSH** > **Auth** in the left navigation pane and configure the SSH private key to use by clicking Browse under Private key file for authentication.

5. Navigate to the location where you saved your SSH private key file, select the file, and click Open. NOTE: You cannot connect while on VPN or in the Oracle office on clear-corporate (choose clear-internet).

   ![img](https://oracle.github.io/learning-library/data-management-library/database/options/img/putty-auth.png)

6. The file path for the SSH private key file now displays in the Private key file for authentication field.

7. Click Session in the left navigation pane, then click Save in the Load, save or delete a stored session section.

8. Click Open to begin your session with the instance.



------

## Run the Setup Scripts

1. Copy the following commands into your terminal. These scripts take approximately 1.5hrs to run. It is a series of scripts that create several databases in multiple ORACLE HOMES so that you can run both the Multitenant and Advanced Multitenant labs. The last two scripts are run in the background so you should be able to exit out while it’s running. The setupdb.sh script takes approximately 25 minutes to run. The setupcdb.sh takes 60 minutes to run, both run in the background.

   Note: If you are running in windows using putty, ensure your Session Timeout is set to greater than 0

   ```
   cd /home/opc/
   wget https://objectstorage.us-phoenix-1.oraclecloud.com/n/oraclepartnersas/b/Multitenant/o/multiscripts.zip
   unzip multiscripts.zip; chmod +x *.sh
   /home/opc/setupenv.sh
   nohup /home/opc/setupdb.sh &> setupdb.out&
   ```



2. To check the status of the script above run the command below. This script takes about 30 minutes to complete. You can also use the unix **jobs** command to see if the script is still running.

   ```
   tail -f /home/opc/setupdb.out
   ```

3. Once the database software has been installed, run the script to create the container databases and pluggable databases needed for the next lab.

   ```
   nohup /home/opc/setupcontainers.sh &> setupcontainers.out&
   ```

4. To check on the progress of this script, enter the command below. This script takes about 60 minutes to complete. Note: Ignore the [WARNING] [DBT-06208] that occur in the 2nd script.

   ```
      tail -f /home/opc/setupcontainers.out
   ```



5. Once the script is finished,
   Congratulations! Now you have the environment to run the Multitenant labs. You may proceed to the [Multitenant Lab](https://oracle.github.io/learning-library/data-management-library/database/options/multitenant.html).



## Introduction

From the point of view of an application, the PDB is the database, in which applications run unchanged. PDBs can be very rapidly provisioned and a pluggable database is a portable database, which makes it very easy to move around, perhaps for load balancing or migration to the Cloud.

Many PDBs can be plugged into a single Multitenant Container Database or CDB. From the point of view of a DBA, the CDB is the database. Common operations are performed at the level of the CDB enabling the DBA to manage many as one for operations such as upgrade, configuration of high availability, taking backups; but we retain granular control when appropriate. This ability to manage many as one enables tremendous gains in operational efficiency.

Enormous gains in technical efficiency are enabled by a shared technical infrastructure. There’s a single set of background processes and a single, global memory area – the SGA – shared by all the PDBs. The result is that with this architecture we can consolidate more applications per server.

[![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/multitenant.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/multitenant.png)



## Setup

### Lab Assumptions

- Each participant has been provided a username and password to a tenancy.
- Each participant has completed the Environment Setup lab.
- Each participant has created an OCI compute instance using the database template.

There are two container databases running:

- CDB1 running on port 1523
- CDB2 running on port 1524

### Lab Setup

All the scripts for this lab are located in the /u01/app/oracle/labs/multitenant folder.

1. To access the scripts, secure shell into the OCI compute instance.

2. Change to the ssh directory and ssh into your instance. The public IP address can be found by going to Compute -> Instance.

   ```
   cd .ssh
   ssh -i optionskey opc@<your public ip address>
   ls
   sudo su - oracle
   cd /home/oracle/labs/multitenant
   ```



## Step 1: Create PDB

This section looks at how to create a new PDB.

The tasks you will accomplish in this lab are:

- Create a pluggable database **PDB2** in the container database **CDB1**

1. Connect to **CDB1**

   ```
   . oraenv
   CDB1
   sqlplus /nolog
   connect sys/oracle@localhost:1523/cdb1 as sysdba
   ```



2. Check to see who you are connected as. At any point in the lab you can run this script to see who or where you are connected.

   ```
   select
     'DB Name: '  ||Sys_Context('Userenv', 'DB_Name')||
     ' / CDB?: '     ||case
       when Sys_Context('Userenv', 'CDB_Name') is not null then 'YES'
         else  'NO'
         end||
     ' / Auth-ID: '   ||Sys_Context('Userenv', 'Authenticated_Identity')||
     ' / Sessn-User: '||Sys_Context('Userenv', 'Session_User')||
     ' / Container: ' ||Nvl(Sys_Context('Userenv', 'Con_Name'), 'n/a')
     "Who am I?"
     from Dual
     /
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/whoisconnected.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/whoisconnected.png)

3. Create a pluggable database **PDB2**.

   ```
   show  pdbs;
   create pluggable database PDB2 admin user PDB_Admin identified by oracle;
   alter pluggable database PDB2 open;
   show pdbs;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/showpdbsbefore.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/showpdbsbefore.png)

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/createpdb.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/createpdb.png)

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/showpdbsafter.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/showpdbsafter.png)

4. Change the session to point to **PDB2**.

   ```
   alter session set container = PDB2;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/altersession.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/altersession.png)

5. Grant **PDB_ADMIN** the necessary privileges and create the **USERS** tablespace for **PDB2**.

   ```
   grant sysdba to pdb_admin;
   create tablespace users datafile size 20M autoextend on next 1M maxsize unlimited segment space management auto;
   alter database default tablespace Users;
   grant create table, unlimited tablespace to pdb_admin;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/grantsysdba.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/grantsysdba.png)

6. Connect as **PDB_ADMIN** to **PDB2**.

   ```
   connect pdb_admin/oracle@localhost:1523/pdb2
   ```

7. Create a table **MY_TAB** in **PDB2**.

   ```
   create table my_tab(my_col number);

   insert into my_tab values (1);

   commit;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/createtable.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/createtable.png)

8. Change back to **SYS** in the container database **CDB1** and show the tablespaces and datafiles created.

   ```
   connect sys/oracle@localhost:1523/cdb1 as sysdba

   COLUMN "Con_Name" FORMAT A10
   COLUMN "T'space_Name" FORMAT A12
   COLUMN "File_Name" FORMAT A120
   SET LINESIZE 220
   SET PAGES 9999

   with Containers as (
     select PDB_ID Con_ID, PDB_Name Con_Name from DBA_PDBs
     union
     select 1 Con_ID, 'CDB$ROOT' Con_Name from Dual)
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Data_Files inner join Containers using (Con_ID)
   union
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Temp_Files inner join Containers using (Con_ID)
   order by 1, 3
   /
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/containers.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/containers.png)

## Step 2: Clone a PDB

This section looks at how to clone a PDB

The tasks you will accomplish in this lab are:

- Clone a pluggable database **PDB2** into **PDB3**

1. Connect to **CDB1**.

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1523/cdb1 as sysdba
   ```



2. Change **PDB2** to read only.

   ```
   alter pluggable database PDB2 open read only force;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/alterplug.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/alterplug.png)

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/showpdbs.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/showpdbs.png)

3. Create a pluggable database **PDB3** from the read only database **PDB2**.

   ```
   create pluggable database PDB3 from PDB2;
   alter pluggable database PDB3 open force;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/createpdb3.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/createpdb3.png)

4. Change **PDB2** back to read write.

   ```
   alter pluggable database PDB2 open read write force;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/pdb2write.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/pdb2write.png)

5. Connect to **PDB2** and show the table **MY_TAB**.

   ```
   connect pdb_admin/oracle@localhost:1523/pdb2
   select * from my_tab;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/pdb2mytab.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/pdb2mytab.png)

6. Connect to **PDB3** and show the table **MY_TAB**.

   ```
   connect pdb_admin/oracle@localhost:1523/pdb3
   select * from my_tab;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/pdb3mytab.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/pdb3mytab.png)

## Step 3: Unplug a PDB

This section looks at how to unplug a PDB

The tasks you will accomplish in this lab are:

- Unplug **PDB3** from **CDB1**

1. Connect to **CDB1**.

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1523/cdb1 as sysdba
   ```

2. Unplug **PDB3** from **CDB1**.

   ```
   show pdbs
   alter pluggable database PDB3 close immediate;

   alter pluggable database PDB3
   unplug into
   '/u01/app/oracle/oradata/CDB1/pdb3.xml';

   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/unplugpdb3.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/unplugpdb3.png)

3. Remove **PDB3** from **CDB1**.

   ```
   drop pluggable database PDB3 keep datafiles;

   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/droppdb3.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/droppdb3.png)

4. Show the datafiles in **CDB1**.

   ```
   COLUMN "Con_Name" FORMAT A10
   COLUMN "T'space_Name" FORMAT A12
   COLUMN "File_Name" FORMAT A120
   SET LINESIZE 220
   SET PAGES 9999

   with Containers as (
     select PDB_ID Con_ID, PDB_Name Con_Name from DBA_PDBs
     union
     select 1 Con_ID, 'CDB$ROOT' Con_Name from Dual)
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Data_Files inner join Containers using (Con_ID)
   union
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Temp_Files inner join Containers using (Con_ID)
   order by 1, 3
   /
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/cdb1data.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/cdb1data.png)

5. Look at the XML file for the pluggable database **PDB3**.

   ```
   host cat /u01/app/oracle/oradata/CDB1/pdb3.xml
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/xmlfile.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/xmlfile.png)

## Step 4: Plug in a PDB

This section looks at how to plug in a PDB

The tasks you will accomplish in this lab are:

- Plug **PDB3** into **CDB2**

1. Connect to **CDB2**

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1524/cdb2 as sysdba

   COLUMN "Who am I?" FORMAT A120
   select
     'DB Name: '  ||Sys_Context('Userenv', 'DB_Name')||
     ' / CDB?: '     ||case
       when Sys_Context('Userenv', 'CDB_Name') is not null then 'YES'
       else 'NO'
       end||
     ' / Auth-ID: '   ||Sys_Context('Userenv', 'Authenticated_Identity')||
     ' / Sessn-User: '||Sys_Context('Userenv', 'Session_User')||
     ' / Container: ' ||Nvl(Sys_Context('Userenv', 'Con_Name'), 'n/a')
     "Who am I?"
   from Dual
   /

   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/whoamicdb2.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/whoamicdb2.png)

2. Check the compatibility of **PDB3** with **CDB2**

   ```
   begin
     if not
       Sys.DBMS_PDB.Check_Plug_Compatibility
       ('/u01/app/oracle/oradata/CDB1/pdb3.xml')
     then
       Raise_Application_Error(-20000, 'Incompatible');
     end if;
   end;
   /
   ```

3. Plug **PDB3** into **CDB2**

   ```
   create pluggable database PDB3
   using '/u01/app/oracle/oradata/CDB1/pdb3.xml'
   move;

   show pdbs
   alter pluggable database PDB3 open;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/createwithxml.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/createwithxml.png)

4. Review the datafiles in **CDB2**

   ```
   COLUMN "Con_Name" FORMAT A10
   COLUMN "T'space_Name" FORMAT A12
   COLUMN "File_Name" FORMAT A120
   SET LINESIZE 220
   SET PAGES 9999


   with Containers as (
     select PDB_ID Con_ID, PDB_Name Con_Name from DBA_PDBs
     union
     select 1 Con_ID, 'CDB$ROOT' Con_Name from Dual)
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Data_Files inner join Containers using (Con_ID)
   union
   select
     Con_ID,
     Con_Name "Con_Name",
     Tablespace_Name "T'space_Name",
     File_Name "File_Name"
   from CDB_Temp_Files inner join Containers using (Con_ID)
   order by 1, 3
   /
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/mtdbfcdb2.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/mtdbfcdb2.png)

5. Connect as **PDB_ADMIN** to **PDB3** and look at **MY_TAB**;

   ```
   connect pdb_admin/oracle@localhost:1524/pdb3

   select * from my_tab;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/pdb3mytab2.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/pdb3mytab2.png)

## Step 5: Drop a PDB

This section looks at how to drop a pluggable database.

The tasks you will accomplish in this lab are:

- Drop **PDB3** from **CDB2**

1. Connect to **CDB2**

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1524/cdb2 as sysdba
   ```

2. Drop **PDB3** from **CDB2**

   ```
   show pdbs

   alter pluggable database PDB3 close immediate;

   drop pluggable database PDB3 including datafiles;

   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/droppdb.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/droppdb.png)

## Step 6: Clone an Unplugged PDB

This section looks at how to create a gold copy of a PDB and clone it into another container.

The tasks you will accomplish in this lab are:

- Create a gold copy of **PDB2** in **CDB1** as **GOLDPDB**
- Clone **GOLDPDB** into **COPYPDB1** and **COPYPDB2** in **CDB2**

1. Connect to **CDB1**

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1523/cdb1 as sysdba
   ```

2. Change **PDB2** to read only

   ```
   alter pluggable database PDB2 open read only force;
   show pdbs
   ```

3. Create a pluggable database **GOLDPDB** from the read only database **PDB2**

   ```
   create pluggable database GOLDPDB from PDB2;
   alter pluggable database GOLDPDB open force;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/goldpdb.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/goldpdb.png)

4. Change **PDB2** back to read write

   ```
   alter pluggable database PDB2 open read write force;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/mountgoldpdb.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/mountgoldpdb.png)

5. Unplug **GOLDPDB** from **CDB1**

   ```
   show pdbs
   alter pluggable database GOLDPDB close immediate;

   alter pluggable database GOLDPDB
   unplug into '/u01/app/oracle/oradata/CDB1/goldpdb.xml';

   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/unpluggold.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/unpluggold.png)

6. Remove **GOLDPDB** from **CDB1**

   ```
   drop pluggable database GOLDPDB keep datafiles;

   show pdbs
   ```

7. Connect to **CDB2**

   ```
   connect sys/oracle@localhost:1524/cdb2 as sysdba
   ```

8. Validate **GOLDPDB** is compatibile with **CDB2**

   ```
   begin
     if not
       Sys.DBMS_PDB.Check_Plug_Compatibility
   ('/u01/app/oracle/oradata/CDB1/goldpdb.xml')
     then
       Raise_Application_Error(-20000, 'Incompatible');
     end if;
   end;
   /
   ```

9. Create a clone of **GOLDPDB** as **COPYPDB1**

   ```
   create pluggable database COPYPDB1 as clone
   using '/u01/app/oracle/oradata/CDB1/goldpdb.xml'
   storage (maxsize unlimited max_shared_temp_size unlimited)
   copy;
   show pdbs
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/clonegold1.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/clonegold1.png)

10. Create another clone of **GOLDPDB** as **COPYPDB2**

    ```
    create pluggable database COPYPDB2 as clone
    using '/u01/app/oracle/oradata/CDB1/goldpdb.xml'
    storage (maxsize unlimited max_shared_temp_size unlimited)
    copy;
    show pdbs
    ```

    [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/clonegold.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/clonegold.png)

11. Open all of the pluggable databases

    ```
    alter pluggable database all open;

    show pdbs
    ```

    [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/allopen.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/allopen.png)

12. Look at the GUID for the two cloned databases

    ```
    COLUMN "PDB Name" FORMAT A20
    select PDB_Name "PDB Name", GUID
    from DBA_PDBs
    order by Creation_Scn
    /
    ```

    [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/guid.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/guid.png)

## Step 7: PDB Hot Clones

This section looks at how to hot clone a pluggable database.

The tasks you will accomplish in this lab are:

- Create a pluggable database **OE** in the container database **CDB1**
- Create a load against the pluggable database **OE**
- Create a hot clone **OE_DEV** in the container database **CDB2** from the pluggable database **OE**

1. Connect to **CDB1**

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1523/cdb1 as sysdba
   ```

2. Create a pluggable database **OE** with an admin user of **SOE**

   ```
   create pluggable database oe admin user soe identified by soe roles=(dba);
   alter pluggable database oe open;
   alter session set container = oe;
   grant create session, create table to soe;
   alter user soe quota unlimited on system;
   ```

   [![ ](https://github.com/oracle/learning-library/raw/master/data-management-library/database/options/img/mt/oe.png)](https://github.com/oracle/learning-library/blob/master/data-management-library/database/options/img/mt/oe.png)

3. Connect as **SOE** and create the **sale_orders** table

   ```
   connect soe/soe@localhost:1523/oe
   CREATE TABLE sale_orders
   (ORDER_ID      number,
   ORDER_DATE    date,
   CUSTOMER_ID   number);
   ```

4. Open a new terminal window, sudo to the oracle user and execute write-load.sh. Leave this window open and running throughout the rest of the multitenant labs.

   ```
   sudo su - oracle
   cd /home/oracle/labs/multitenant
   ./write-load.sh
   ```

   Leave this window open and running for the next few labs.

5. Go back to your original terminal window. Connect to **CDB2** and create the pluggable **OE_DEV** from the database link **oe@cdb1_link**

   ```
   connect sys/oracle@localhost:1524/cdb2 as sysdba
   create pluggable database oe_dev from oe@cdb1_link;
   alter pluggable database oe_dev open;
   ```

6. Connect as **SOE** to **OE_DEV** and check the number of records in the **sale_orders** table

   ```
   connect soe/soe@localhost:1524/oe_dev
   select count(*) from sale_orders;
   ```

7. Connect as **SOE** to **OE** and check the number of records in the **sale_orders** table

   ```
   connect soe/soe@localhost:1523/oe
   select count(*) from sale_orders;
   ```

8. Close and remove the **OE_DEV** pluggable database

   ```
   connect sys/oracle@localhost:1524/cdb2 as sysdba
   alter pluggable database oe_dev close;
   drop pluggable database oe_dev including datafiles;
   ```

9. Leave the **OE** pluggable database open with the load running against it for the rest of the labs.

You can see that the clone of the pluggable database worked without having to stop the load on the source database. In the next lab you will look at how to refresh a clone.

## Step 8: PDB Refresh

This section looks at how to hot clone a pluggable database, open it for read only and then refresh the database.

The tasks you will accomplish in this lab are:

- Leverage the **OE** pluggable database from the previous lab with the load still running against it.
- Create a hot clone **OE_REFRESH**` in the container database **CDB2** from the pluggable database **OE**
- Refresh the **OE_REFRESH**` pluggable database.

1. Connect to **CDB2**

   ```
   sqlplus /nolog
   connect sys/oracle@localhost:1524/cdb2 as sysdba
   ```

2. Create a pluggable database **OE_REFRESH**` with manual refresh mode from the database link **oe@cdb1_link**

   ```
   create pluggable database oe_refresh from oe@cdb1_link refresh mode manual;
   alter pluggable database oe_refresh open read only;
   ```

3. Connect as **SOE** to the pluggable database **OE_REFRESH**` and count the number of records in the sale_orders table

   ```
   conn soe/soe@localhost:1524/oe_refresh
   select count(*) from sale_orders;
   ```

4. Close the pluggable database **OE_REFRESH**` and refresh it from the **OE** pluggable database

   ```
   conn sys/oracle@localhost:1524/oe_refresh as sysdba

   alter pluggable database oe_refresh close;

   alter session set container=oe_refresh;
   alter pluggable database oe_refresh refresh;
   alter pluggable database oe_refresh open read only;
   ```

5. Connect as **SOE** to the pluggable dataabse **OE_REFRESH**` and count the number of records in the **sale_orders** table. You should see the number of records change.

   ```
   conn soe/soe@localhost:1524/oe_refresh
   select count(*) from sale_orders;
   ```

6. Close and remove the **OE_DEV** pluggable database

   ```
   conn sys/oracle@localhost:1524/cdb2 as sysdba

   alter pluggable database oe_refresh close;
   drop pluggable database oe_refresh including datafiles;
   ```

7. Leave the **OE** pluggable database open with the load running against it for the rest of the labs.

## Step 9: PDB Relocation

This section looks at how to relocate a pluggable database from one container database to another. One important note, either both container databases need to be using the same listener in order for sessions to keep connecting or local and remote listeners need to be setup correctly. For this lab we will change **CDB2** to use the same listener as **CDB1**.

The tasks you will accomplish in this lab are:

- Change **CDB2** to use the same listener as **CDB1**
- Relocate the pluggable database **OE** from **CDB1** to **CDB2** with the load still running
- Once **OE** is open the load should continue working.

1. Change **CDB2** to use the listener **LISTCDB1**

   ```
   sqlplus /nolog
   conn sys/oracle@localhost:1524/cdb2 as sysdba;
   alter system set local_listener='LISTCDB1' scope=both;
   ```

2. Connect to **CDB2** and relocate **OE** using the database link **oe@cdb1_link**

   ```
   conn sys/oracle@localhost:1523/cdb2 as sysdba;
   create pluggable database oe from oe@cdb1_link relocate;
   alter pluggable database oe open;
   show pdbs
   ```

3. Connect to **CDB1** and see what pluggable databases exist there

   ```
   conn sys/oracle@localhost:1523/cdb1 as sysdba
   show pdbs
   ```

4. Close and remove the **OE** pluggable database

   ```
   conn sys/oracle@localhost:1523/cdb2 as sysdba

   alter pluggable database oe close;
   drop pluggable database oe including datafiles;
   ```

5. The load program isn't needed anymore and that window can be closed.

6. If you are going to continue to use this environment you will need to change **CDB2** back to use **LISTCDB2**

   ```
   sqlplus /nolog
   conn sys/oracle@localhost:1523/cdb2 as sysdba;
   alter system set local_listener='LISTCDB2' scope=both;
   ```

## Conclusion

Now you've had a chance to try out the Multitenant option. You were able to create, clone, plug and unplug a pluggable database. You were then able to accomplish some advanced tasks that you could leverage when maintaining a large multitenant environment.Hands-on with Multitenant
