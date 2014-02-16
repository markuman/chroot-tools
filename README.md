chroot-tools
============

1. `superchroot` – full automatic chroot helper
2. `getchroot` – download and extract prepared folder installation of sevaral linux distributions. 
  * supported linux distributions are: Arch Linux, Ubuntu (12.04, 13.04), Debian (Wheezy, Sid), Fedora
3. `createchroot`– creates a folder install for sevaral linux distributions  
  * supported linux distributions are: Arch Linux, Ubuntu (12.04, 13.04), Debian (Wheezy, Sid)


# Dependencies

* wget
* fakechroot _(optional)_
* fakeroot _(optional)_
* [proot](http://proot.me/) _(optional)_

## ToDo

* figure out better proot usage
* improve `createchroot` and write a small dokumentation
* sumcheck for existing file -> compare to mirror -> redownload?
* improvements for ubuntu debootstraps and the dash error messages

## current status - 1.0

* Debian (sid + wheezy), Arch and Fedora images are fine. For Ubuntu contrainers you may get `dash` errors on non `dash` systems.
* Take care for network connectivity in your chroot. `echo "nameserver 8.8.8.8 > /etc/resolv.conf`


# superchroot

`systemd-nspawn`, `arch-chroot` and `chroot` needs root previleges (automatically selected if user is root).  
`fakechroot` is the first choice if you use it as unprevilege user. Due different glibc versions this might be only work if you try to chroot the same system 
as your host system.


        superchroot <folder>
        superchroot [chroot-option] <folder>

        [chroot options]: systemd (root), chroot (root), fakechroot, proot, exit (umount directories from chroot methode)


        EXAMPLES - force method
        ~$: su -c "superchroot systemd arch/"
        ~$: sudo superchroot chroot arch/
        ~$: superchroot proot arch/
        ~$: superchroot fakechroot arch/

        EXAMPLES - automaticaly best choice
        ~#: superchroot arch/
        ~$: superchroot arch/



### As user – fakechroot

        host	image>>         Raring          Precise         Wheezy          Fedora         Arch
     
        Saucy         		  yes               no              no              yes            no
        Precise        		  no                yes             no              no             no
        Wheezy        		  no                no              yes             no             no
        Fedora        		  no                no              no              yes            no
        Arch          		  no                no              no              no             yes

There are glibc conflicts if host system != chroot system. So for example on Fedora Host, setting up a Fedora chroot.

    ~$: getchroot fedora ~/testchroot && superchroot ~/testchroot 

You can create a user like `useradd -m -g users -s /bin/bash maxpower` and login with `su maxpower`. If you do `su - maxpower` you'll be
kicked out of the chroot and the boundaries between chroot and host system are blurred!!

The process of the fakechroot is called `/usr/bin/faked-sysv`. So you can renice it etc. pp.



### As user – proot (experimental)

This should work with every host system when proot is installed. But you have only read/run access and are not able to modify the chroot.
Some work is needed to create a edit-able workdirectory for prooted chroot environments.

# getchroot
They are downloaded automaticaly to `~/.getchroot/` and will be extracted to the given folder.  
Furthermore, `getchroot proot` will download a precompiled proot binary to `~/.bin/proot/` and add it to your ~/.bashrc path.

        ./getchroot --help
        ./getchroot <distribution> <folder>

        <distribution>: arch wheezy sid percise raring fedora
                This will download a prepared tar.xz file to ~/.getchroot/ and extract to your given folder


        EXAMPLES

        :~$ ./getchroot arch mychrootarch
        This will download arch.tar.xz to ~/.getchroot/ if not available and extract it to mychrootarch/

	:~$ ./getchroot proot
        This will download a precompiled proot binary for x86_64 to ~/.bin/proot/ and add this path to your ~/.bashrc

The fact that the containers are tared as root user, you might be get some extract error for `dev/` devices. This should have no impact!

#### About container files (*.tar.xz)


* For Arch Linux: simply `pacstrap -dGM <folder>` is used
  * First you have to setup pacman. `pacman-key --init && pacman-key --populate archlinux` and setup the Mirrorlist!
* For Debian/Ubuntu: simplay `debootstrap <release> <folder> <mirror>` is used
* For Fedora

		mkdir -p /chroot/devel/var/lib/rpm
		rpm --root /chroot/devel --initdb
		yumdownloader --destdir=/tmp fedora-release
		rpm --root /chroot/devel -ivh /tmp/fedora-release*rpm
		yum --installroot=/chroot/devel install bash yum
		yum --installroot=/chroot/devel groupinstall "minimal install"





# createchroot (experimental)

This will setup the chroot folder install on your system.  
This work without root privileges only on Debian-based systems. 

	~$: ./createchroot proot
	This will git clone the proot source, try to build it to ~/.bin/proot/ and add this path to your ~/.bashrc path

# DEBIAN WHEEZY EXAMPLE

NOTE: no root privileges are needed as long as fakechroot is installed


	markuman@octave-de:~/tmp/chroot-tools$ cat /etc/os-release 
	PRETTY_NAME="Debian GNU/Linux 7 (wheezy)"
	NAME="Debian GNU/Linux"
	VERSION_ID="7"
	VERSION="7 (wheezy)"
	ID=debian
	ANSI_COLOR="1;31"
	HOME_URL="http://www.debian.org/"
	SUPPORT_URL="http://www.debian.org/support/"
	BUG_REPORT_URL="http://bugs.debian.org/"
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ # INSTALL WHEEZY CHROOT WITHOUT ROOT PRIVILEGS #
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ ./createchroot wheezy/ 
	I: Retrieving Release
	I: Retrieving Release.gpg
	I: Checking Release signature
	I: Valid Release signature (key id ED6D65271AACF0FF15D123036FB2A1C265FFB764)
	I: Retrieving Packages
	I: Validating Packages
	I: Resolving dependencies of required packages...
	I: Resolving dependencies of base packages...
	...
	I: Configuring aptitude...
	I: Configuring tasksel...
	I: Configuring tasksel-data...
	I: Base system installed successfully.
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ # now superchroot it
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ ./superchroot wheezy/
	fakechroot found...
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/# # LET'S DO APT-GET UPDATE AND INSTALL HTOP #
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/# apt-get update
	Hit http://cdn.debian.net wheezy Release.gpg
	Hit http://cdn.debian.net wheezy Release
	Hit http://cdn.debian.net wheezy/main amd64 Packages
	Get:1 http://cdn.debian.net wheezy/main Translation-en [3,849 kB]
	Fetched 3,849 kB in 2s (1,512 kB/s)         
	Reading package lists... Done
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/# htop
	bash: /usr/bin/htop: No such file or directory
	root@octave-de:/# apt-get install htop
	Reading package lists... Done
	Building dependency tree... Done
	Suggested packages:
	  strace ltrace
	The following NEW packages will be installed:
	  htop
	0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
	Need to get 74.9 kB of archives.
	After this operation, 216 kB of additional disk space will be used.
	Get:1 http://cdn.debian.net/debian/ wheezy/main htop amd64 1.0.1-1 [74.9 kB]
	Fetched 74.9 kB in 0s (399 kB/s)
	Selecting previously unselected package htop.
	(Reading database ... 9297 files and directories currently installed.)
	Unpacking htop (from .../htop_1.0.1-1_amd64.deb) ...
	Processing triggers for man-db ...
	Setting up htop (1.0.1-1) ...
	root@octave-de:/# 
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/# 
	root@octave-de:/# htop --version
	htop 1.0.1 - (C) 2004-2011 Hisham Muhammad
	Released under the GNU GPL.
	
	root@octave-de:/#
	root@octave-de:/# exit
	exit
	markuman@octave-de:~/tmp/chroot-tools$ 
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ # LET'S GET SID #
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ ./getchroot sid sid/
	--2014-02-16 15:48:56--  http://getchroot.osuv.de/sid.tar.xz
	Resolving getchroot.osuv.de (getchroot.osuv.de)... 164.138.25.251, 2a02:2770::21a:4aff:fe34:f698
	Connecting to getchroot.osuv.de (getchroot.osuv.de)|164.138.25.251|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 109898428 (105M) [application/octet-stream]
	Saving to: `/home/markuman/.getchroot/sid.tar.xz'
	
	100%[=============================================================================================================================>] 109,898,428 	33.0M/s   in 3.2s    
	
	2014-02-16 15:49:00 (33.0 MB/s) - `/home/markuman/.getchroot/sid.tar.xz' saved [109898428/109898428]
	
	tar: dev/ram14: Cannot mknod: Operation not permitted
	...
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ # LET'S TRY SUPERCHROOT #
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ ./superchroot sid/
	fakechroot found...
	
	/usr/sbin/chroot: error while loading shared libraries: __vdso_time: invalid mode for dlopen(): Invalid argument
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ # NOW WITH ROOT PRIVILEGS #
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ su -c "./superchroot sid/"
	Password: 
	
	Running classic chroot with mount -t... 
	
	
	bash: no job control in this shell

	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/#
	root@octave-de:/# cat /etc/os-release 
	PRETTY_NAME="Debian GNU/Linux jessie/sid"
	NAME="Debian GNU/Linux"
	ID=debian
	ANSI_COLOR="1;31"
	HOME_URL="http://www.debian.org/"
	SUPPORT_URL="http://www.debian.org/support/"
	BUG_REPORT_URL="http://bugs.debian.org/"
        root@octave-de:/#
        root@octave-de:/# exit
        exit
        markuman@octave-de:~/tmp/chroot-tools$
        markuman@octave-de:~/tmp/chroot-tools$
        markuman@octave-de:~/tmp/chroot-tools$ # LET'S GET FEDORA #
        markuman@octave-de:~/tmp/chroot-tools$
        markuman@octave-de:~/tmp/chroot-tools$
	markuman@octave-de:~/tmp/chroot-tools$ ./getchroot fedora fedora/
	--2014-02-16 15:54:18--  http://getchroot.osuv.de/fedora.tar.xz
	Resolving getchroot.osuv.de (getchroot.osuv.de)... 164.138.25.251, 2a02:2770::21a:4aff:fe34:f698
	Connecting to getchroot.osuv.de (getchroot.osuv.de)|164.138.25.251|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 138901036 (132M) [application/octet-stream]
	Saving to: `/home/markuman/.getchroot/fedora.tar.xz'
	
	100%[=============================================================================================================================>] 138,901,036 42.7M/s   in 3.1s    
	
	2014-02-16 15:54:21 (42.7 MB/s) - `/home/markuman/.getchroot/fedora.tar.xz' saved [138901036/138901036]
	
	markuman@octave-de:~/tmp/chroot-tools$ su -c "./superchroot fedora/"
	Password: 
	
	Running classic chroot with mount -t... 
	
	
	bash: no job control in this shell
	[root@octave-de /]#
	[root@octave-de /]# 
	[root@octave-de /]# ###### WELCOME TO FEDORA :) #
	[root@octave-de /]#
	[root@octave-de /]# yum update
	No packages marked for update
	[root@octave-de /]# echo "nameserver 8.8.8.8" > /etc/resolv.conf
	[root@octave-de /]# yum install htop
	Resolving Dependencies
	--> Running transaction check
	---> Package htop.x86_64 0:1.0.2-2.fc19 will be installed
	--> Finished Dependency Resolution
	
	Dependencies Resolved
	
	=======================================================================================================================================================================
	 Package                              Arch                                   Version                                      Repository                              Size
	=======================================================================================================================================================================
	Installing:
	 htop                                 x86_64                                 1.0.2-2.fc19                                 fedora                                  78 k
	
	Transaction Summary
	=======================================================================================================================================================================
	Install  1 Package
	
	Total download size: 78 k
	Installed size: 165 k
	Is this ok [y/d/N]: y
	
	Downloading packages:
	Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
	htop-1.0.2-2.fc19.x86_64.rpm                                                                                                                    |  78 kB  
	00:00:00     
	Running transaction check
	Running transaction test
	Transaction test succeeded
	Running transaction
	  Installing : htop-1.0.2-2.fc19.x86_64                                                                                                                            
	1/1 
	  Verifying  : htop-1.0.2-2.fc19.x86_64                                                                                                                            
	1/1 
	
	Installed:
	  htop.x86_64 0:1.0.2-2.fc19                                                                                                                                           
	
	Complete!
	[root@octave-de /]# 
	[root@octave-de /]#
	[root@octave-de /]#
	[root@octave-de /]# cat /etc/os-release 
	NAME=Fedora
	VERSION="19 (Schrödinger’s Cat)"
	ID=fedora
	VERSION_ID=19
	PRETTY_NAME="Fedora 19 (Schrödinger’s Cat)"
	ANSI_COLOR="0;34"
	CPE_NAME="cpe:/o:fedoraproject:fedora:19"
	HOME_URL="https://fedoraproject.org/"
	BUG_REPORT_URL="https://bugzilla.redhat.com/"
	REDHAT_BUGZILLA_PRODUCT="Fedora"
	REDHAT_BUGZILLA_PRODUCT_VERSION=%{bug_version}
	REDHAT_SUPPORT_PRODUCT="Fedora"
	REDHAT_SUPPORT_PRODUCT_VERSION=%{bug_version}
	[root@octave-de /]# 

