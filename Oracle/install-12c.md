Notes on Oracle 12c Installation
--------------------------------

These are helper notes for migration from 11g to 12c **[see 11g notes][1]**. Much of the migration is scripted; however, since various systems (such as QA) have drifted over time, many steps are manual so that a sanity check can be performed before continuing on to the next step.

## Stage Software assets for Oracle 12c Installation

Make sure the Oracle installer binaries are accessible from the 'oracle' account.  It is a good idea to check the Oracle Support website to see if an PatchSet is available for 12c.  If so, download and install from that.  As well, we'll be using an Oracle 12c response file located in the 'centos-setup-pa' repository, so make sure that repo is current:

 * Login as `oracle` user
 * git-pull-all

## Produce **Gold** backup from existing Oracle 11g Installation

 * On PROD during the final conversion, we'll manually run `production-expdp.sh` to make sure we've captured all recent database activity within the backup (using the Oracle `expdp` utility.)
 * On QA and BU, we'll simply use the `prime.dmp.gz` backup file delivered during the last run of the production utility. Since QA and BU are always behind PROD this presents no problems.
 * We'll also need to manually create a `prime.dmp.signal` file on PROD since that file is required for the import utility to complete, but it's only delivered to remote systems.
 * On systems such as BU, we'll want to disable the daily CRON job which imports production backups using `crontab -r`
 * On systems such as PROD, QA, and BU we'll want to make a complete backup of existing `/opt/oracle` directory using the following commands as `root`

     # cd /opt
     # tar zcf ~/oracle.tar.gz oracle

## Shutdown Tomcat and Oracle services

We'll want Oracle 11g off during the Deinstall step.  As well, we don't want these services to come back on during boot. As `root`:

     # chkconfig tomcatd off
     # chkconfig oracle off
     # service tomcatd stop
     # service oracle stop

## Deinstall the existing Oracle 11g Database Software

Before we install Oracle 12c, we must first completely un-install Oracle 11g.  By doing this we can reuse our database name, `orcl`, as well as the network ports accessed via JDBC driver configurations (1521.)  As `oracle`, perform the following actions:

 * `cd /opt/oracle/product`
 * `ls -l` (you should see a directory there named 11.2.0)
 * `11.2.0/db_1/deinstall/deinstall`
     * Accept the default components to be removed ('LISTENER' and 'orcl')
     * Answer 'no' to "Do you still want to modify the details of orcl database(s)?
     * Answer 'yes' to "Do you want to continue?" after CCR check is finished.
     * Once completed, you will get a message to 'rm -f /etc/oraInst.loc' as root.
     * Go ahead and 'rm -f /etc/oratab' as well, since this will be re-created.

## Update key Oracle configuration files

Here are the configuration files that need to be updated to point to the 'new' Oracle Home location (as 'root'):

 * Old Oracle Home: `/opt/oracle/product/11.2.0/db_1`
 * New Oracle Home: `/opt/oracle/product/12.1.0/db_1`

 * `/etc/init.d/oracle` - run the centos-setup-pa/oracle-setup/04-e-mkInit.sh script to create a new, better version
 * `/etc/profile.d/oracle.sh`
 * `/etc/oratab` - this file will be created by new install

**NOTE:** Be sure to logout and log back in so the environment variables above are available during the install (next step.)

## Run the customized Oracle 12c installer script

 * Verify your `oracle` login shell is in the Oracle 12c binary installation directory (the one with the `runInstaller` program)
 * Execute ${HOME}/centos-setup-pa/oracle-setup/07-runInstaller.sh
     * Click "Next" confirming you're ignoring software updates.
     * Accept to Create and configure a database
     * Accept a Server class system
     * Accept a Single instance database installation
     * Choose "Advanced" install and accept "English" language
     * Choose "Standard Edition One"
     * Accept ORACLE_BASE=/opt/oracle and ORACLE_HOME=/opt/oracle/product/12.1.0/db_1 values
     * Accept Inventory and group names, confirming the Central Inventory is located in ORACLE_BASE
     * Accept General Purpose system.
     * Accept 'orcl' as SID name, but UNCHECK "Create as Container database"
     * Accept Memory, Character Sets, but UNCHECK "Create database with Sample Schemas"
     * Accept File system location (/opt/oracle/oradata)
     * Do not Register with EM Cloud Control
     * Accept Enable Recovery
     * Accept and Confirm same password for all accounts.
     * Accept all group settings.
     * Click Install!
     * Execute the following scripts as root:
         * /opt/oracle/oraInventory/orainstRoot.sh
         * /opt/oracle/product/12.1.0/db_1/root.sh
     * Next the "Oracle Configuration Assistant" will begin
         * Accept all defaults, click "Close" to exit.
         * Edit '/etc/oratab' and change "N" to "Y"

## Create the new **PADATA** tablespace

Since we're making big changes, we'll take this opportunity to create a single tablespace to be used by our database (named **PADATA**.)  Run the following script as the `oracle` user:

 * ${HOME}/centos-setup-pa/oracle-setup/08-modTableSpace.sh

## Run the Oracle Migration Import script (only done once!)

This migration script is *special* in that it will re-map legacy tablespace names into a new name, **PADATA**, which will now contain all database objects. This will greatly reduce the complexity of maintaining the Oracle Database.

 * ${HOME}/centos-setup-pa/oracle-maint/migrate-tablespace-impdp.sh

## Double-check all key Oracle configuration files

 * /etc/init.d/oracle
 * /etc/oratab
 * /etc/profile.d/oracle.sh

## Startup Tomcat and Oracle services

To restore the system to normal operation, and make sure our services come back on during boot, as `root`:

     # chkconfig tomcatd on ; service tomcatd start
     # chkconfig oracle on

Until the `prime.dmp` files coming from production are made using the new Oracle 12c and tablespace, you'll need to run the `migrate-tablespace-impdp.sh` script so the tablespaces get re-mapped correctly.  Once the `prime.dmp` file is created with Oracle 12c and the new tablespace, then the original `production-impdp.sh` script can used to import on QA and BU.

  [1]: install-11g.md
