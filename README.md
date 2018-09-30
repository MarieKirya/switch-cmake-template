Switch CMake Template
=====================

Introduction
------------

This template exists to compile Switch applications through CMake build system. 
The motivation for doing so was to allow Switch ports to be pulled back into the
main repository and contribute back. It also is cleaner to use than the original
switch template provided by devkitpro.

To clarify this is purely for developers only and is not for end users. 
__There will be no releases.__

To use
------

To use this template, copy it without it's .git folder (but leave the .gitignore 
file for proper version control) and then begin modifying your source files. When
you are ready to setup your compiling, run `./cmake_devkitA64.sh` from the root to 
generate your make file, CMake helpers, and packaging script. The reason cmake is 
run through this shell script is to export all the devkitA64 CC and CXX configurations
into environment variables before cmake is run, this is a helper script for this.

Then to compile simply run `make`. Finally to package up your app
into all it's various variations, including nro, run `./package_switch.sh`.

To modify things like the name it compiles as, or any of the packaging info, you 
can edit CMakeLists.txt. The places to edit are quite clear. Then you can simply run 
`make` again and it will pick up on changes to CMakeLists.txt. If it doesn't, you 
can delete CMakeCache.txt and rerun `./cmake_devkitA64.sh`

__There are two targets currently__, most people won't need this so remove one you don't 
need, either C or C++ one depending on your situation.

Contributing and Bug Reporting
------------------------------

Please use GitHubs native issues and pull request functionality.

Thanks to
---------
DevKit Pro and libnx devs for building these tools.
