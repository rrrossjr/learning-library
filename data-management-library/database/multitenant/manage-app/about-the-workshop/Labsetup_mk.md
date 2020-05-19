#  Multitenant Workshop setup

## OCI cloud account

This Lab uses Linux server with Oracle Software from Oracle Marketplace. We can get to this image by logging into Oracle Cloud.  The Lab is self driven and can the done in the following ways.

- Using Always Free account
- Using Your Oracle Cloud account


#### Always Free Account
If you are using the always free account, the free account gives access to Autonomous database and one OCPU VM server. For more information check out the  **[link](https://docs.cloud.oracle.com/en-us/iaas/Content/FreeTier/resourceref.htm)**. on how to get it.

#### Oracle Cloud Account
If you already have Cloud Account, then you will already have tenant name, username and password needed to login and provision a workshop server.


------------------------------------------------------------------------



## Generate an SSH Key Pair

If you already have an ssh key pair, you may use that to connect to your environment. Based on your laptop config, choose the appropriate step to connect to your instance.
For for information check out the **[link](https://github.com/oracle/learning-library/blob/master/common/labs/generate-ssh-key/generate-ssh-keys.md)**.
 The link also describes how to connect to Linux server once its provisioned. We will need to revist this once we have provisioned the SSH keys.




------



## Provision VM with19c Database software from Oracle Marketplace

From a browser go to **[ Workshop link]( https://cloudmarketplace.oracle.com/marketplace/listing/74094332)**.

![](images/GetApp.png " ")

Pick Region and Click **Sign In**
![](images/signIn.png " ")

Pick the latest version, and accept the "terms and conditions" and Click  **Launch Instance**
![](images/LaunchInstance.png " ")

Under "Create Compute Instance" enter the name for the workshop instance.
![](images/createName.png " ")

Add the **ssh** public key  and click **create**
![](images/addSSH.png " ")



That is it. We have successfully create a Multitenant Workshop Instance.
Check out the IP address of the provisioned server and ssh to it.
If you need help to configure client from Mac or Putty client to this server, please


<img src="../images/createName.png" style="zoom:80%;" />

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
