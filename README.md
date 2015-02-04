grsec custom kernels
====================


custom grsec lean kernels suitable for standard virtualization 

guest kernels are lean and are supposed to be used for server instances.
- works for centos 7 (not tested with other distros).
- No module loading (so that direct boot from kvm can be used without needing to install modules, init ramdisks, ...).
- support for only virtualized hardware provided by kvm/xen
- no usb, pluggable stuff, ...
- a few unneeded things are enabled because of my use cases but they can be removed (eg. nfs server/client).

notes:
- kvmguest: selinux still enabled to ease transition.
- xenguest-linode: as the name implies, works on a linode host. No selinux


building environment
--------------------

the cern devtoolset-2 rpms have to be installed to be able to use the gcc plugins needed by grsecurity (grsecurity would compile without it though).

at the time of writing, it's only available for centos 6 so you'll need to set a centos 6 build environment in order to build kernels for centos 7.

http://linux.web.cern.ch/linux/devtoolset/


building
--------

as usual - unpack kernel, patch with grsec, copy config

in scripts/kconfig/Makefile, add -ltinfo to HOSTLOADLIBES_mconf:

	HOSTLOADLIBES_mconf   = $(shell $(CONFIG_SHELL) $(check-lxdialog) -ldflags $(HOSTCC)) -ltinfo

you'll _need_ to specify the path devtoolset's binaries before building

	PATH=/opt/rh/devtoolset-2/root/usr/bin/:$PATH
	export PATH
	export LDFLAGS=-L/opt/rh/devtoolset-2/root/usr/lib
	export CPPFLAGS=-isystem/opt/rh/devtoolset-2/root/usr/include
	export CC=/opt/rh/devtoolset-2/root/usr/bin/gcc
	export CPP=/opt/rh/devtoolset-2/root/usr/bin/cpp
	export CXX=/opt/rh/devtoolset-2/root/usr/bin/c++

	make

