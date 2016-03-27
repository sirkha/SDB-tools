SDB-tools
=========

Package manager and other tools for Source Distro BLUE.

Executables
-----------
* green: fetch a package and change to the root directory to allow for changes
   prior to building
* blue: build, install and configure the specified package
* black: fetch a package then build install and configure without making changes
* red: uninstall a package
* white: upgrade packages


Source Distro BLUE
==================
Concept
-------
Source Distro BLUE (SDB) will be built around a minimal base system (such as the
[LinuxFromScratch][1] completed build) and a set of generic source build
scripts and binary installation scripts maintained on a public git repository

with a list of scripts and options for those scripts for each package. Each user
will have their own fork of the package description files and build scripts that
they can optionally modify to suite their needs or switch to different upstreams
based on their preference. The package manager, when installing will create a
local fork for EVERY INSTALLED PACKAGE that the user can modify as they wish.
This distro will be designed for people who like to have their environment
differ from upstream and will be highly configurable and highly customizable.

Why???
------
This concept was born from my frustration with Gentoo Linux, and its fragmented
nature that uses overlays to use different package sources. Overlays to me seem
like a way to strap on a solution to the existing Gentoo infrastructure, and I
see better ways to accomplish the use of multiple sources. Additionally, Git
has, in many ways, changed the way versioning works. No longer are we stuck with
a single developer incrementing their own versions, now we have 20 forks of the
same product each with its own advantages and disadvantages. SDB aims to take
advantage of this, and doesn't apologize for making asking the user to make
critical decisions about the software on their machine. Though defaults can be
provided for packaging decisions, SDB by design, has no central repository. The
source of those packaging decisions is up to the user.

Motto
-----
If you don't like it, hack it.

Base File System
----------------
* [Standard Linux File System][2]
* /usr/sbd/ Directory in which the system Git Repositories, listed below, are
   stored.
* /usr/src/ Repositories for each installed package are located here.
* /usr/build/ A directory in which the build script keeps the output files of a
   build for future use.
* /var/build/ A temporary directory in which the build script checks out the 
   source from /usr/src/ and builds it.

System Git Repositories
-----------------------
SDB uses Git repositories wherever possible for configuration of the build
system. Each phase of installation has its own git repository that can be
selected on a per package basis in the JSON package set file.

* SDB-Packages: This repository contains the JSON files that describe the
   available packages and the options for the fetch and build scripts needed to
   initially install a package.
* SDB-Fetch: This repository contains all the scripts needed to fetch packages
   using Git, source tarballs, .deb, or any other package distribution system.
* SDB-Build: This repository contains build scripts that follow many of the
   standard installation tools. The idea is to be agnostic when managing
   packages. A big advantage here is that we let programs like ruby-gems and pip
   manage their own installation methods.
* SDB-Config: This repository contains any configuration files necessary for a
   package. Files here will be the basis for what is in /etc and the user's home
   directory, if the package doesn't provide a default or the distro maintainers
   want to change it.
* SDB-Tools: This repository contains the source for the package manager and other
   required tools.

Package Files
-------------
Each package file in the SDB-Package contains JSON that describes the options
for the fetch and build scripts needed to initially install a package or a set
of packages. This includes dependencies on other packages. This is the best
place to maintain long term control over the upstream source for files in
/usr/src/, as the build system will generally try and merge the local forks
to what is specified here. The package manager, however, will always use the
latest committed code in /usr/src/ to compile and install the package. 

Fetch Scripts
-------------
These scripts take the JSON object from the package file and put the source in
the /usr/src/ directory. If possible, this will result in an initiated Git
Repository. If that doesn't work for the type of fetch being performed, it may
do something else, but the result has to be something that a build script can
use.

Examples:

* Git fetch/checkout
* Source tarball download -> git init.
* RPM/Deb source file extraction
* CVS checkout

Functions:

* Download the package
* Create something to edit in /usr/src/
* Copy /usr/src/ to /var/build/
* Update /usr/src/ when requested by the package manager.

Build Scripts
-------------
Many build systems are available for developers to use. Sometimes it may be
ideal to install parts of software with another package manager, build scripts
will allow for this.

Examples:

* autoconf
* RPM binary installation
* ruby-gems
* pip

Functions:

* Set up build environment based on JSON options
* run necessary build and install steps
* track differences before and after installation
* run necessary uninstall steps
* For binary package managers, dupe the package manager in to knowing about
   what has been installed using other methods.



--------------------------------------------------------------------------------

<!--- References -->
[1]: http://www.linuxfromscratch.org/lfs "Linux From Scratch"
[2]: http://refspecs.linuxfoundation.org/fhs.shtml
    "Filesystem Hierarchy Standard" 
