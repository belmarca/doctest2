---
title: Accessing the system files
---

Files related to Gambit, such as executables, libraries and header files, are
stored in multiple Gambit installation directories. Gambit may be installed on a
system according to two different installation models.

In the first model there is a single directory where all the Gambit installation
directories are stored. This central installation directory is typically
`/usr/local/Gambit` under UNIX, `/Library/Gambit` under macOS and `C:/Program
Files/Gambit` under Microsoft Windows. This may have been overridden when the
system was built with the command `configure --prefix=/my/Gambit`. If the system
was built with the command `configure --enable-multiple-versions` then the
central installation directory is prefix/version, where version is the system
version string (e.g. v4.9.4 for Gambit v4.9.4). Moreover, prefix/current will be
a symbolic link which points to the central installation directory. In this
model, the Gambit installation directory named X is simply the subdirectory X of
the central installation directory.

In the second model some or all of the Gambit installation directories are
stored in installation specific directories. The location of these directories
is assigned when the system is built using the command `configure
--bindir=/my/bin --includedir=/my/include --libdir=/my/lib`.

The advantage of the first model is that it is easy to have multiple versions of
Gambit coexist and to remove all the files of a given version. However, the
second model may be necessary to conform to the package installation conventions
of some operating systems.

Executable programs such as the interpreter gsi and compiler gsc can be found in
the bin installation directory. Adding this directory to the PATH environment
variable allows these programs to be started by simply entering their name. This
is done automatically by the macOS and Microsoft Windows installers.

The runtime library is located in the lib installation directory. When the
system’s runtime library is built as a shared-library (with the command
`configure --enable-shared`) all programs built with Gambit, including the
interpreter and compiler, need to find this library when they are executed and
consequently this directory must be in the path searched by the system for
shared-libraries. This path is normally specified through an environment
variable which is `LD_LIBRARY_PATH` on most versions of UNIX, `LIBPATH` on AIX,
`SHLIB_PATH` on HPUX, `DYLD_LIBRARY_PATH` on macOS, and `PATH` on Microsoft Windows.
If the shell is `sh`, the setting of the path can be made for a single execution
by prefixing the program name with the environment variable assignment, as in:

```shell
$ LD_LIBRARY_PATH=/usr/local/Gambit/lib gsi
```

A similar problem exists with the Gambit header file `gambit.h`, located in the
include installation directory. This header file is needed for compiling Scheme
programs with the Gambit compiler. When the C compiler is being called
explicitly it may be necessary to use a `-I<dir>` command line option to indicate
where to find header files and a `-L<dir>` command line option to indicate where
to find libraries.

Access to both of these files can be simplified by creating a link to them in
the appropriate system directories (special privileges may however be required):

```shell
$ ln -s /usr/local/Gambit/lib/libgambit.a /usr/lib # name may vary
$ ln -s /usr/local/Gambit/include/gambit.h /usr/include
```

Alternatively these files can be copied or linked in the directory where the C
compiler is invoked (this requires no special privileges).

Another approach is to set some environment variables which are used to tell the
C compiler where to find header files and libraries. For example, the following
settings can be used for the gcc C compiler:

```shell
 	
$ export LIBRARY_PATH=/usr/local/Gambit/lib
$ export CPATH=/usr/local/Gambit/include
```

Note that this may have been done by the installation process. In particular,
the macOS and Microsoft Windows prebuilt installers set up the environment so
that the gcc compiler finds these files automatically.
