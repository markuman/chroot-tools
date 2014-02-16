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

Debian (sid + wheezy), Arch and Fedora images are fine. For Ubuntu contrainers you may get `dash` errors on non `dash` systems.


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


        ./getchroot --help
        ./getchroot <distribution> <folder>

        <distribution>: arch wheezy sid percise raring fedora
                This will download a prepared tar.xz file to ~/.getchroot/ and extract to your given folder


        EXAMPLES

        :~$ ./getchroot arch mychrootarch
        This will download arch.tar.xz to ~/.getchroot/ if not available and extract it to mychrootarch/

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



