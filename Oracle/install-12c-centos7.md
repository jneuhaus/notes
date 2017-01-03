Notes on Oracle 12c Installation on CentOS7
-------------------------------------------

Make sure to install the following:

	# yum install zlib zlib-devel

During the linking phase, you will see the following error.

    Error in invoking target 'links proc gen_pcscfg procob' of makefile
    '/u01/app/oracle/product/12.1.0.2/db_1/precomp/lib/ins_precomp.mk'.
    See
    '/u01/app/oraInventory/logs/installActions2014-10-29_09-07-03PM.log'
    for details.

To fix it, do the following:

    rm -rf $ORACLE_HOME/lib/stubs/*
    cp $ORACLE_HOME/rdbms/lib/env_rdbms.mk $ORACLE_HOME/rdbms/lib/env_rdbms.mk.orig

Perform the following modifications:

    $ vi $ORACLE_HOME/rdbms/lib/env_rdbms.mk
    # Line 176
    # FROM:
    LINKTTLIBS=$(LLIBCLNTSH) $(ORACLETTLIBS) $(LINKLDLIBS)
    # TO  :
    LINKTTLIBS=$(LLIBCLNTSH) $(ORACLETTLIBS) $(LINKLDLIBS) -lons
    # Line 279-280
    # FROM:
    LINK=$(FORT_CMD) $(PURECMDS) $(ORALD) $(LDFLAGS) $(COMPSOBJS)
    LINK32=$(FORT_CMD) $(PURECMDS) $(ORALD) $(LDFLAGS32) $(COMPSOBJS)
    # TO  :
    LINK=$(FORT_CMD) $(PURECMDS) $(ORALD) $(LDFLAGS) $(COMPSOBJS) --no-as-needed
    LINK32=$(FORT_CMD) $(PURECMDS) $(ORALD) $(LDFLAGS32) $(COMPSOBJS) --no-as-needed
    # Line 3041-3042
    # FROM:
    TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
            $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS)
    # TO  :
    TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
            $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS) -lnnz12

Click the “Retry” button.

Now, the installation should continue.
