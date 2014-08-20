Notes on Oracle 12c Installation
--------------------------------

These are helper notes for migration from 11g to 12c **[see 11g notes][1]**. Much of the migration is scripted; however, since various systems (such as QA) have drifted over time, many steps are manual so that a sanity check often needs to be performed before continuing on to the next step.

[TOC]

## Stage Software assets for Oracle 12c Installation

Make sure installer binaries are accessible from the 'oracle' account. As well, we'll be using an Oracle 12c response file located in the 'centos-setup-pa' repository, so make sure that repo is current:

 1. Login as 'oracle' user
 2. git-pull-all

## Produce **Gold** backup from existing Oracle 11g Installation

 1. On PROD during the final conversion, we'll manually run 'production-expdp.sh' to make sure we've captured all recent database activity within the backup (using the Oracle 'expdp' utility.)
 2. On QA and BU, we'll simply use the 'prime.dmp.gz' backup file delivered during the last run of the production utility. Since QA and BU are always behind PROD this presents no problems.
 3. We'll also need to manually create a 'prime.dmp.signal' file on PROD since that file is required for the import utility to complete, but it's only delivered to remote systems.

## Shutdown Tomcat and Oracle services

 1. So service don't come back on during boot, as root:
     2. chkconfig tomcatd off
     3. chkconfig oracle off
 4. We'll want Oracle 11g off during the Deinstall step, as root:
     5. service tomcatd stop
     6. service oracle stop

## Deinstall the existing Oracle 11g Database Software

Before we install Oracle 12c, we must first completely un-install Oracle 11g.  By doing this we can reuse our database name, 'orcl', as well as the network ports accessed via JDBC driver configurations (1521.)  As 'oracle', perform the following actions:

 1. cd /opt/oracle/product
 2. ls -l (you should see a directory there named 11.2.0)
 3. 11.2.0/db_1/deinstall/deinstall
     4. Accept the default components to be removed ('LISTENER' and 'orcl')
     5. Answer 'no' to "Do you still want to modify the details of orcl database(s)?
     6. Answer 'yes' to "Do you want to continue?" after CCR check is finished.
     7. Once completed, you will get a message to 'rm -f /etc/oraInst.loc' as root.
     8. Go ahead and 'rm -f /etc/oratab' as well, since this will be re-created.

## Update key Oracle configuration files

Here are the configuration files that need to be updated to point to the 'new' Oracle Home location:

  * Old Oracle Home: /opt/oracle/product/11.2.0/db_1
  * New Oracle Home: /opt/oracle/product/12.1.0/db_1

 1. /etc/init.d/oracle
 2. /etc/profile.d/oracle.sh
 3. /etc/oratab (this file will be created by new install)

NOTE: Be sure to logout and log back in so the environment variables above are available during the install (next step.)

## Run the customized Oracle 12c installer script

 1. Make sure your 'oracle' login shell is in the Oracle 12c binary installation directory (the one with the 'runInstaller' program.)
 2. Execute ${HOME}/centos-setup-pa/oracle-setup/07-runInstaller.sh
    1. Click "Next" confirming you're ignoring software updates.
    2. Accept to Create and configure a database
    3. Accept a Server class system
    4. Accept a Single instance database installation
    5. Choose "Advanced" install and accept "English" language
    6. Choose "Standard Edition One"
    7. Accept ORACLE_BASE=/opt/oracle and ORACLE_HOME=/opt/oracle/product/12.1.0/db_1 values
    8. Accept Inventory and group names, confirming the Central Inventory is located in ORACLE_BASE
    9. Accept General Purpose system.
    10. Accept 'orcl' as SID name, but UNCHECK "Create as Container database"
    11. Accept Memory, Character Sets, but UNCHECK "Create database with Sample Schemas"
    12. Accept File system location (/opt/oracle/oradata)
    13. Do not Register with EM Cloud Control
    14. Accept Enable Recovery
    15. Accept and Confirm same password for all accounts.
    16. Accept all group settings.
    17. Click Install!
    18. Execute the following scripts as root:
        1. /opt/oracle/oraInventory/orainstRoot.sh
        2. /opt/oracle/product/12.1.0/db_1/root.sh
    19. Next the "Oracle Configuration Assistant" will begin
        1. Accept all defaults, click "Close" to exit.
        2. Edit '/etc/oratab' and change "N" to "Y"

## Create the new **PADATA** tablespace

  1. Run the following script as the 'oracle' user:
    1. ${HOME}/centos-setup-pa/oracle-setup/08-modTableSpace.sh

## Run the Oracle Migration Import script (only done once!)

This migration script is "special" in that it will re-map legacy tablespace names into a new name, **PADATA**, which will now contain all database objects. This will greatly reduce the complexity of maintaining the Oracle Database.

  1. ${HOME}/centos-setup-pa/oracle-maint/migrate-tablespace-impdp.sh

## Double-check all key Oracle configuration files

  1. /etc/init.d/oracle
  2. /etc/oratab
  3. /etc/profile.d/oracle.sh

## Startup Tomcat and Oracle services

 1. So services come back on during boot, as root:
     2. chkconfig tomcatd on ; service tomcatd start
     3. chkconfig oracle on

  [1]: install-11g.md
