chroot-tools
============

1. `superchroot` – full automatic chroot helper
2. `getchroot` – download and extract prepared folder installation of sevaral linux distributions. 
  * supported linux distributions are: 
    * Arch Linux
    * Ubuntu Precise (12.04)
    * Ubuntu Raring (13.04)
    * Debian 6 (squeeze)
    * Debian 7 (Wheezy)
    * Debian Sid 
    * Fedora 19 (Schrödinger's cat)
    * Fedora 20
  * download precompiled proot binary for x86_64
3. `createchroot`– creates a folder install for sevaral linux distributions  
  * supported linux distributions are: 
    * Arch Linux 
    * Ubuntu Precise (12.04) 
    * Ubuntu Raring (13.04) 
    * Debian 7 (Wheezy)
    * Debian Sid
  * compile proot and install it to ~/.bin/proot/


# Dependencies

* wget
* awk
* curl
* bash _(recommended)_
* fakechroot _(optional)_
* fakeroot _(optional)_
* [proot](http://proot.me/) _(optional)_

## ToDo

* figure out better proot usage
* improve `createchroot` and write a small dokumentation
* improvements for ubuntu debootstraps needed and the handle dash error messages!
  * any ideas?
* fix systemd-nspawn für squeeze (add /etc/os-release file)
* add/write/test graphical access

## current status 

* Debian, Arch and Fedora images are fine. For Ubuntu contrainers you may get `dash` errors on non `dash` systems.

# Documentation / Usage

For more use `--help` commands or read the [Wiki](https://github.com/markuman/chroot-tools/wiki).
