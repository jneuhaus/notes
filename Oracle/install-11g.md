## Notes on Oracle 11g Installation ##

According to the Oracle Release Notes, The following packages (or later versions)
must be installed on Red Hat Linux, so they were installed with yum as noted.

    binutils-2.15.92.0.2-10.EL4 (already installed)
    compat-db-4.1.25-9          (installed with "yum install compat-db")
    control-center-2.8.0-12     (installed with "yum install control-center" -- see below)
    gcc-3.4.3-9.EL4             (already installed)
    gcc-c++-3.4.3-9.EL4         (already installed)
    glibc-2.3.4-2               (already installed)
    glibc-common-2.3.4-2        (already installed)
    gnome-libs-1.4.1.2.90-44.1  (installed with "yum install gnome-libs" -- see below)
    libstdc++-3.4.3-9.EL4       (already installed)
    libstdc++-devel-3.4.3-9.EL4 (already installed)
    make-3.80-5                 (already installed)
    pdksh-5.2.14-30             (not installed)
    sysstat-5.0.5-1             (installed with "yum install sysstat")
    xscreensaver-4.18-5.rhel4.2 (not installed)
    libaio-0.3.104-2            (installed with "yum install libaio")

Installation of the package "control-center" requires XWindows, which was not
installed by default for the iWeb server, therefore, installing this package
required the following packages to also be installed:

     Package                 Arch       Version          Repository        Size
    =============================================================================
    Installing:
     control-center          i386       1:2.10.1-6       base              2.2 M
    Installing for dependencies:
     GConf2                  i386       2.10.0-4         base              1.3 M
     ORBit2                  i386       2.12.1-3         base              234 k
     alsa-lib                i386       1.0.9rf-2.FC4    updates-released  361 k
     at-spi                  i386       1.6.3-1          base              224 k
     atk                     i386       1.9.1-1          base              178 k
     audiofile               i386       1:0.2.6-2        base               98 k
     cdrecord                i386       8:2.01.1-9.0.FC4.1  updates-released  573 k
     chkfontpath             i386       1.10.0-4         base               13 k
     desktop-backgrounds-basic  noarch     2.0-29           base              2.6 M
     docbook-dtds            noarch     1.0-26           base              663 k
     eel2                    i386       2.10.0-2         base              381 k
     esound                  i386       1:0.2.36-0.fc4.1  updates-released  127 k
     evolution-data-server   i386       1.2.3-3.fc4      updates-released  3.5 M
     gail                    i386       1.8.3-2          base              325 k
     gamin                   i386       0.1.1-3.FC4      updates-released  101 k
     ghostscript             i386       7.07-41          updates-released  7.6 M
     ghostscript-fonts       noarch     5.50-13          base              808 k
     gnome-desktop           i386       2.10.0-5         base              650 k
     gnome-icon-theme        noarch     2.10.1-4         base              3.0 M
     gnome-keyring           i386       0.4.2-1          base              120 k
     gnome-menus             i386       2.10.1-3         base               62 k
     gnome-mime-data         i386       2.4.2-1          base              679 k
     gnome-panel             i386       2.10.1-10.2      updates-released  2.7 M
     gnome-vfs2              i386       2.10.0-5         base              1.1 M
     gnome-vfs2-smb          i386       2.10.0-5         base               29 k
     gtk2                    i386       2.6.10-2.fc4.4   updates-released  4.8 M
     hicolor-icon-theme      noarch     0.8-2            base               24 k
     howl                    i386       0.9.8-3          base              105 k
     howl-libs               i386       0.9.8-3          base               85 k
     intltool                i386       0.33-2           base              109 k
     libIDL                  i386       0.8.5-2          base               77 k
     libart_lgpl             i386       2.3.17-2         base               69 k
     libbonobo               i386       2.8.1-1          base              436 k
     libbonoboui             i386       2.8.1-4          base              356 k
     libcroco                i386       0.6.0-5          base              113 k
     libexif                 i386       0.6.12-3         base               97 k
     libgail-gnome           i386       1.1.0-5          base               26 k
     libglade2               i386       2.5.1-2          base               92 k
     libgnome                i386       2.10.0-3         base              705 k
     libgnomecanvas          i386       2.10.0-1         base              210 k
     libgnomecups            i386       0.2.0-2          base               70 k
     libgnomeprint22         i386       2.10.3-1         base              344 k
     libgnomeprintui22       i386       2.10.1-1         base              268 k
     libgnomeui              i386       2.10.0-1         base              848 k
     libgsf                  i386       1.11.1-2         base               75 k
     librsvg2                i386       2.9.5-2          base              150 k
     libsoup                 i386       2.2.3-4.FC4      updates-released  121 k
     libwnck                 i386       2.10.3-1         updates-released  151 k
     libxklavier             i386       2.0-1            base               77 k
     libxslt                 i386       1.1.14-2         base              469 k
     metacity                i386       2.10.3-1         updates-released  1.9 M
     mkisofs                 i386       8:2.01.1-9.0.FC4.1  updates-released  573 k
     mozilla-nspr            i386       37:1.7.12-1.5.1  updates-released  111 k
     mozilla-nss             i386       37:1.7.12-1.5.1  updates-released  684 k
     nautilus                i386       2.10.0-4         base              3.7 M
     nautilus-cd-burner      i386       2.10.0-2         base              256 k
     openjade                i386       1.3.2-16         base              1.9 M
     pango                   i386       1.8.1-2          base              272 k
     redhat-artwork          i386       0.122-10         base              4.6 M
     samba-common            i386       3.0.14a-2        base              5.7 M
     scrollkeeper            i386       0.3.14-5         base              291 k
     sgml-common             noarch     0.6.3-17         base               41 k
     shared-mime-info        i386       0.16-3           base              133 k
     startup-notification    i386       0.8-2            base               28 k
     ttmkfdir                i386       3.0.9-16.1       updates-released   49 k
     urw-fonts               noarch     2.3-1            base              4.5 M
     xml-common              noarch     0.6.3-17         base              5.8 k
     xorg-x11-font-utils     i386       6.8.2-37.FC4.49.2  updates-released  124 k
     xorg-x11-xfs            i386       6.8.2-37.FC4.49.2  updates-released  141 k
     xscreensaver-base       i386       1:4.21-4         base              724 k

Installation of the package "gnome-libs" required the following prerequisites
to be intalled:

     Package                 Arch       Version          Repository        Size
    =============================================================================
    Installing:
     gnome-libs              i386       1:1.4.1.2.90-46  base              1.0 M
    Installing for dependencies:
     ORBit                   i386       1:0.5.17-15      base              313 k
     gdk-pixbuf              i386       1:0.22.0-18.fc4.2  updates-released  225 k
     glib                    i386       1:1.2.10-16      base              130 k
     gtk+                    i386       1:1.2.10-39      base              885 k
     imlib                   i386       1:1.9.13-24      base              409 k
     libpng10                i386       1.0.18-2         base              149 k
     libungif                i386       4.1.3-3.fc4.2    updates-released   39 k

Installed xorg-x11 package, which is required for the Oracle Installer, and
this package also required the following:

     Package                 Arch       Version          Repository        Size
    =============================================================================
    Installing:
     xorg-x11                i386       6.8.2-37.FC4.49.2  updates-released   14 M
    Installing for dependencies:
     fonts-xorg-base         noarch     6.8.2-1          base              7.3 M
     xinitrc                 noarch     4.0.18.1-1       updates-released   28 k
     xorg-x11-xauth          i386       6.8.2-37.FC4.49.2  updates-released  102 k
     xterm                   i386       205-1.FC4        updates-released  175 k

According to http://www.puschitz.com/InstallingOracle10g.shtml the following
packages must also be installed for Fedora Core 4.

     Package                 Arch       Version          Repository        Size
    =============================================================================
    Installing:
     compat-libstdc++-33       i386       3.2.3-47.fc4       base              228 k
     openmotif                 i386       2.2.3-10.FC4.1     updates-released  1.4 M
     xorg-x11-deprecated-libs  i386       6.8.2-37.FC4.49.2  updates-released   88 k
     openmotif21               i386       2.1.30-14          base              996 k


Updated Kernel parameters by modifying the file /etc/sysctl.conf per Oracle
installation recommendations.

    kernel.shmall = 2097152
    kernel.shmmax = 2147483648
    kernel.shmmni = 4096
    kernel.sem = 250 32000 100 128
    fs.file-max = 65536
    net.ipv4.ip_local_port_range = 1024 65000
    rmem_default = 262144
    rmem_max = 262144
    wmem_default = 262144
    wmem_max = 262144

Updated the file /etc/security/limits.conf according to Oracle recommendations
by adding the following lines for the "oracle" user.

    oracle           soft    nproc   2047
    oracle           hard    nproc   16384
    oracle           soft    nofile  1024
    oracle           hard    nofile  65536

After running the Oracle Universal Installer, the following are setup:

    iSQL*Plus URL:       http://hd-t3333cl.privatedns.com:5560/isqlplus
    iSQL*Plus DBA URL:   http://hd-t3333cl.privatedns.com:5560/isqlplus/dba
    Enterprise Manager:  http://hd-t3333cl.privatedns.com:1158/em

To Start and Stop the Oracle Web-based Administration servers, as the oracle
user, execute the following commands:

    isqlplusctl stop | start
    emctl stop dbconsole | start dbconsole

Added user accounts to be members of the Oracle groups (/etc/group) so commands
like sqlplus will work when logged into the system:

    dba:x:11011:oracle,jboss,elindquist,blane,jkirkwood,sshadmin
    oinstall:x:11012:oracle,jboss,elindquist,blane,jkirkwood,sshadmin

modified $ORACLE_HOME/sqlplus/admin/glogin.sql (the sqlplus startup script and added: DEFINE _EDITOR=vi

Created PMP and PADOC tablespaces

    create tablespace pmp datafile '/home/oracle/oracle/product/10.2.0/oradata/orcl/pmp01.dbf' size 2g
    create tablespace pmp datafile '/home/oracle/oracle/product/10.2.0/oradata/orcl/pmp02.dbf' size 2g
    create tablespace padoc datafile '/home/oracle/oracle/product/10.2.0/oradata/orcl/padoc01.dbf' size 100M

Created PMP, PAWEB and PADOC users:

    create user pmp identified by ** default tablespace pmp temporary tablespace temp quota unlimited on pmp
    create user paweb identified by ** default tablespace pmp temporary tablespace temp quota unlimited on pmp
    create user padoc identified by ** default tablespace padoc temporary tablespace temp quota unlimited on padoc
    grant connect to pmp
    grant connect to paweb
    grant connect to padoc

Created PMPUSER and PSTUSER but did NOT grant connect privileges; these were "readonly" users that were granted SELECT privileges but not UPDATE they need to be created so the import process doesn't show a ton of error messages when it tries to grant privileges:

    create user pmpuser identified by **
    create user pstuser identified by **




