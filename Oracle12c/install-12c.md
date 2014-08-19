## Notes on Oracle 12c Installation ##
### These are helper notes for migration from 11g to 12c **[see 11g notes][1]** ###

 1. Produce **Gold** backup from existing Oracle 11g Installation
 2. Shutdown Tomcat and Oracle services
 3. Deinstall the existing Oracle 11g Database Software
 4. Update key Oracle configuration files
 5. Run the customized Oracle 12c installer script
 6. Create the new <code>PADATA</code> tablespace
 7. Run the <code>migrate-tablespace-impdp.sh</code> script
 8. Double-check all key Oracle configuration files


  [1]: install-11g.md