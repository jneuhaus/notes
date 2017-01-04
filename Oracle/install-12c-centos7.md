Notes on Oracle 12c Installation on CentOS7
-------------------------------------------

Make sure to install the following:

	# yum install zlib zlib-devel

Configuration file for a CentOS 7 system:

	# cat /etc/sysctl.conf
	kernel.shmall = 1073741824
	kernel.shmmax = 4398046511104
	kernel.shmmni = 4096
	kernel.sem = 250 32000 100 128
	fs.aio-max-nr = 1048576
	fs.file-max = 6815744
	net.ipv4.ip_local_port_range = 9000 65500
	net.core.rmem_default = 262144
	net.core.rmem_max = 4194304
	net.core.wmem_default = 262144
	net.core.wmem_max = 1048586
	vm.swappiness = 10
	vm.vfs_cache_pressure = 50
	vm.hugetlb_shm_group = 506

During the linking phase, if you see the following error.

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
    # Line 3041-3042
    # FROM:
    TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
            $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS)
    # TO  :
    TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
            $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS) -lnnz12

Click the “Retry” button.

Now, the installation should continue.
