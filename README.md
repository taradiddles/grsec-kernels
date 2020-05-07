grsec custom kernels
====================

!! OUTDATED !! - haven't used those since the grsec project closed access to their kernel patch, so use at your own risk.

what's here ?
--------------

- a shell script that automates the building steps: download grsec patch + kernel, check sigs, compile, ...
- sample lean .config for kvm and xen guests, with grsec enabled.

various notes
-------------

guest kernels are lean and are supposed to be used for server instances.
- works for centos 7 (not tested with other distros).
- no module loading (so that direct boot from kvm can be used without needing to install modules, init ramdisks, ...).
- support only virtualized hardware provided by kvm/xen
- no usb, pluggable stuff, ...
- a few unneeded things are enabled because of my use cases but they can be removed (eg. nfs server/client).
- kvmguest: selinux still enabled to ease transition.
- xenguest-linode: as the name implies, works on a linode host. Selinux enabled (change from previous version).

other sites of interest:
- https://github.com/el-grsecurity
- http://pkg.tag1consulting.com/kernel/el6/x86_64 


building environment
--------------------

the cern devtoolset-2 rpms have to be installed to be able to use the gcc plugins needed by grsecurity (grsecurity would compile without it though).

at the time of writing, it's only available for centos 6 so you'll need to set a centos 6 build environment in order to build kernels for centos 7.

http://linux.web.cern.ch/linux/devtoolset/

an alternative is to use a recent distribution, like fedora


building
--------

you can use the build_latest shell script to automate building, it will automatically fetch the latest kernel and grsecurity patches, and optionaly build a kernel with a supplied .config file

otherwise the manual steps are:

as usual - unpack kernel, patch with grsec, copy config

if you use 'make menuconfig' and get an error, add -ltinfo to HOSTLOADLIBES_mconf in scripts/kconfig/Makefile:

	HOSTLOADLIBES_mconf   = $(shell $(CONFIG_SHELL) $(check-lxdialog) -ldflags $(HOSTCC)) -ltinfo

don't forget to specify the path devtoolset's binaries before building (better to include that in your build user's bashrc).

	PATH=/opt/rh/devtoolset-2/root/usr/bin/:$PATH
	export PATH
	export LDFLAGS=-L/opt/rh/devtoolset-2/root/usr/lib
	export CPPFLAGS=-isystem/opt/rh/devtoolset-2/root/usr/include
	export CC=/opt/rh/devtoolset-2/root/usr/bin/gcc
	export CPP=/opt/rh/devtoolset-2/root/usr/bin/cpp
	export CXX=/opt/rh/devtoolset-2/root/usr/bin/c++

