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
If you need help to configure client from Mac or Putty client to this server, please check the **[link](https://github.com/oracle/learning-library/blob/master/common/labs/generate-ssh-key/generate-ssh-keys.md)** if you need help.

The default user is "opc" and accessed by ssh private key. It does not have a password.


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
