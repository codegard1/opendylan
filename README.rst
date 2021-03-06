Welcome to Open Dylan!
======================

Open Dylan is a compiler and a set of libraries for the `Dylan
programming language <http://opendylan.org/books/drm>`_.  If you're
interested in working on the compiler and core libraries then you've
come to the right place.  If you want to write your own Dylan
libraries and *use* the compiler then you should `download a binary
release <http://opendylan.org/download/>`_ and then read the `Getting
Started
guide <http://opendylan.org/documentation/getting-started-cli/>`_.

Open Dylan is written in Dylan, thus a Dylan compiler is needed to
bootstrap it. Binary releases are available from
http://opendylan.org/download/.

Verify that the downloaded version is working correctly by building
a hello-world binary::

  make-dylan-app hello-world
  cd hello-world
  dylan-compiler -build hello-world
  _build/bin/hello-world

Note: If there is no _build directory already, dylan-compiler will
create it and build all used libraries.  Subsequent builds will be
much faster.


Building Open Dylan
===================

Clone the git repository::

  git clone git://github.com/dylan-lang/opendylan.git --recursive

It does not work to download a ZIP file of the repository from github
because it doesn't include git submodules.


UNIX
====

Please note that on 64 bit Linux the default stack is too small for
Open Dylan so it must be increases with ``ulimit -s``. It's usually
safe to double its value.

Dependencies
------------

Get MPS or boehm-gc, depending on your platform:

* Linux x86 or FreeBSD x86 (LLVM back-end) -> `MPS 1.114
  <http://www.ravenbrook.com/project/mps/release/1.114.0/>`_
* macOS and all 64 bit (C back-end) -> `boehm-gc
  <https://github.com/ivmai/bdwgc>`_

The ``libunwind`` library is an optional dependency on Linux and
FreeBSD. If available, it is used to display stack traces for
unhandled error conditions. (The ``libunwind`` API is built-in on
macOS.)

On macOS, you may find it easiest to install Homebrew and install
the following::

    brew install autoconf automake bdw-gc --universal

You will also need to install the command line build tools available from
Apple. If your installation of ``bdw-gc`` is not universal (doesn't contain
both i386 and x86_64 code), you will need to uninstall it and install again
with the ``--universal`` flag.

On Ubuntu, Debian, etc, you can install the necessary dependencies
with::

    apt-get install autoconf automake gcc libgc-dev libunwind-dev

Building
--------

To build ``dylan-compiler``, ``make-dylan-app``, and several tools::

  export PATH=$(dirname $(which dylan-compiler)):$PATH
  ./autogen.sh
  ./configure --prefix=/opt/opendylan-current   # (but see note below)
  make
  sudo make install

The build process attempts to select the correct garbage collector
implementation based on your platform.

If you are on ``x86-linux`` or ``x86-freebsd`` you must add a flag to
``configure`` to point it at the MPS sources, using ``--with-mps``::

  ./configure --prefix=/opt/opendylan-current --with-mps=/path/to/mps

``/path/to/mps`` should point to the root directory of the MPS
distribution, for example ``--with-mps=/opt/mps-kit-1.114.0``.

On other platforms, the Boehm GC will be used. If you have installed the
Boehm GC via your operating system package manager, you may not need to
specify the location of the Boehm GC. It will be found automatically if
it is in ``/usr`` or ``/usr/local``. If you have installed the Boehm GC
into a non-standard location or the configure script can not find it,
you can point it in the right direction by using ``--with-gc``::

  ./configure --prefix=/opt/opendylan-current --with-gc=/path/to/boehm

By default, this will build a fully bootstrapped compiler with the
first generation in ``Bootstrap.1/bin/dylan-compiler``, the second
generation in ``Bootstrap.2/bin/dylan-compiler``, and the third in
``Bootstrap.3/bin/dylan-compiler``. The third generation will then be
installed as ``/opt/opendylan-current/bin/dylan-compiler``.

Make sure that the target installation directory has been deleted. If you try
to install into a directory that already has an older version of Open Dylan in
it, the build will fail.

Running Tests
-------------

There is an extensive set of tests which can be run once the build is
complete::

  make check

This runs the tests for the core language implementation as well as for many
bundled libraries.  However, there are currently many test failures which
need to be fixed. Most of the test failures are minor issues or are due to
unimplemented tests rather than major bugs. Help is welcome in improving
our test suites.

Windows
=======

* Get `MPS 1.108
  <http://www.ravenbrook.com/project/mps/release/1.108.0/>`_. Be sure
  that you have the older 1.108 release and **not** the newer 1.114
  release.

* Make sure to have required tools installed: namely Debugging tools for
  Windows, a C compiler (PellesC or VC6) and Microsoft Platform SDK.

* Open a shell (windows command processor) and set the environment
  variable SDK4MEMORY_POOL_SYSTEM to <where you unpacked MPS>.

* Please keep in mind that paths with whitespaces are not well supported.

* cd into admin\\builds and run::

    build-release.bat <target-dir> /sources <git-checkout>\sources /internal

This will do a 4-stage bootstrap.  In the end there will be a complete
IDE in <target-dir>.

* Building an installer:

  * Get NSIS from http://nsis.sf.net and the HTML help workshop (from
    Microsoft, to generate the chm).

  * Go to packages\\win32-nsis, read Build.txt and follow the
    instructions. Make sure you are using the same command shell as
    used for building Open Dylan (to retain environment variables).
