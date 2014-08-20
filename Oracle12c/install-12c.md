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

## Run the customized Oracle 12c installer script

## Create the new **PADATA** tablespace

## Run the **migrate-tablespace-impdp.sh** script

## Double-check all key Oracle configuration files

## Startup Tomcat and Oracle services

 1. So services come back on during boot, as root:
     2. chkconfig tomcatd on
     3. chkconfig oracle on

  [1]: install-11g.md