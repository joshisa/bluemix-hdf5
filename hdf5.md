
# Example Notebook for Setting up and Installing H5PY on Bluemix

## Identify your current working directory
### We will need to declare a few paths


```python
# Author:  Sanjay Joshi (@jStartter) ibm.biz/sanjay_joshi
# Courtesy of jStart - IBM Emerging Technology's client engagement team

import subprocess as sub
p = sub.Popen(['pwd'],stdout=sub.PIPE,stderr=sub.PIPE)
prefix, errors = p.communicate()
prefix = os.sep.join(prefix.split(os.sep)[:-2])
shareDir = prefix + "/.local/share"
hdf5Dir = shareDir + "/notebook_hdf5"
print "prefix = " + prefix
print "shareDir = " + shareDir
print "hdf5Dir = " + hdf5Dir
```

    prefix = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5
    shareDir = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share
    hdf5Dir = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5



```python
# Temp Testing cell to help reset setup
# Uncomment and Run if you want to test modifications/tweaks to this notebook and need to reset to an uninstalled state
# !rm -rf $shareDir
# !rm -rf $prefix/.local/lib/python2.7/site-packages/h5py
# !rm -rf $prefix/.local/lib/python2.7/site-packages/h5py-2.6.0-py2.7.egg-info
# !rm -rf *
```


```python
!mkdir $shareDir
!mkdir $hdf5Dir
!mkdir $hdf5Dir/hdf5
```

## Fetch Snapshot of hdf5 tar gzip file


```python
!wget https://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz -O $hdf5Dir/hdf5-1.8.17.tar.gz
```

    --2016-05-17 16:53:51--  https://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz
    Resolving www.hdfgroup.org (www.hdfgroup.org)... 50.28.50.143
    Connecting to www.hdfgroup.org (www.hdfgroup.org)|50.28.50.143|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 12304149 (12M) [application/x-gzip]
    Saving to: '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz'
    
    100%[======================================>] 12,304,149  2.22MB/s   in 5.4s   
    
    2016-05-17 16:53:56 (2.17 MB/s) - '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz' saved [12304149/12304149]
    


## Untar (extract) file


```python
# This command takes about 10-15 secs
!tar -zxvf $hdf5Dir/hdf5-1.8.17.tar.gz -C $hdf5Dir >/dev/null
hdf5SrcDir = hdf5Dir + "/hdf5-1.8.17"
print hdf5SrcDir
```

    /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17



```python
!ls -al $hdf5SrcDir
```

    total 2376
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 .
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:53 ..
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users     683 Apr 26 07:44 ACKNOWLEDGMENTS
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    5701 Apr 26 07:44 CMakeFilters.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   24952 Apr 26 07:44 CMakeInstallation.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   40635 Apr 26 07:44 CMakeLists.txt
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    4677 Apr 26 07:44 COPYING
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    1508 Apr 26 07:44 CTestConfig.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   68969 May  6 16:41 MANIFEST
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    1318 Apr 26 07:44 Makefile
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    7599 Apr 26 07:44 Makefile.am
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    1318 Apr 26 07:44 Makefile.dist
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   37714 May 10 16:24 Makefile.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    1755 May 10 16:31 README.txt
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    1089 Apr 26 07:44 UserMacros.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  352306 May 10 16:24 aclocal.m4
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    4014 Apr 26 07:44 acsite.m4
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:53 bin
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 c++
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 config
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users 1034850 May 10 16:24 configure
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  105916 May 10 16:24 configure.ac
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 examples
    drwxr-xr-x  6 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 fortran
    drwxr-xr-x  8 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 hl
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:53 m4
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 release_docs
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   16384 May 17 16:54 src
    drwxr-xr-x  3 s1a2-472d95bcebf7db-bf066087ecf5 users    8192 May 17 16:54 test
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 testpar
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 16:54 tools



```python
# Setup Configuration Info
!$hdf5SrcDir/configure --prefix=$hdf5Dir/hdf5
```

    checking for a BSD-compatible install... /bin/install -c
    checking whether build environment is sane... yes
    checking for a thread-safe mkdir -p... /bin/mkdir -p
    checking for gawk... gawk
    checking whether make sets $(MAKE)... yes
    checking whether make supports nested variables... yes
    checking whether make supports nested variables... (cached) yes
    checking whether to enable maintainer-specific portions of Makefiles... no
    checking build system type... x86_64-unknown-linux-gnu
    checking host system type... x86_64-unknown-linux-gnu
    checking shell variables initial values... done
    checking if basename works... yes
    checking if xargs works... yes
    checking for cached host... none
    checking for config x86_64-unknown-linux-gnu... no
    checking for config x86_64-unknown-linux-gnu... no
    checking for config unknown-linux-gnu... no
    checking for config unknown-linux-gnu... no
    checking for config x86_64-linux-gnu... no
    checking for config x86_64-linux-gnu... no
    checking for config x86_64-unknown... no
    checking for config linux-gnu... found
    compiler 'gcc' is GNU gcc-4.8.3
    checking for config /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/config/site-specific/host-yp-spark-dal09-env5-0024... no
    checking for gcc... gcc
    checking whether the C compiler works... yes
    checking for C compiler default output file name... a.out
    checking for suffix of executables... 
    checking whether we are cross compiling... no
    checking for suffix of object files... o
    checking whether we are using the GNU C compiler... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to accept ISO C89... none needed
    checking whether gcc understands -c and -o together... yes
    checking for style of include used by make... GNU
    checking dependency style of gcc... gcc3
    checking if unsupported combinations of configure options are allowed... no
    checking if Fortran interface enabled... no
    checking if Fortran 2003 interface enabled... no
    checking whether we are using the GNU C++ compiler... yes
    checking whether g++ accepts -g... yes
    checking dependency style of g++... gcc3
    checking how to run the C++ preprocessor... g++ -E
    checking if c++ interface enabled... no
    checking if high level library is enabled... yes
    checking for perl... perl
    checking for ar... ar
    checking whether make sets $(MAKE)... (cached) yes
    checking for tr... /bin/tr
    checking if srcdir= and time commands work together... no
    checking how to print strings... printf
    checking for a sed that does not truncate output... /bin/sed
    checking for grep that handles long lines and -e... /bin/grep
    checking for egrep... /bin/grep -E
    checking for fgrep... /bin/grep -F
    checking for ld used by gcc... /bin/ld
    checking if the linker (/bin/ld) is GNU ld... yes
    checking for BSD- or MS-compatible name lister (nm)... /bin/nm -B
    checking the name lister (/bin/nm -B) interface... BSD nm
    checking whether ln -s works... yes
    checking the maximum length of command line arguments... 1572864
    checking whether the shell understands some XSI constructs... yes
    checking whether the shell understands "+="... yes
    checking how to convert x86_64-unknown-linux-gnu file names to x86_64-unknown-linux-gnu format... func_convert_file_noop
    checking how to convert x86_64-unknown-linux-gnu file names to toolchain format... func_convert_file_noop
    checking for /bin/ld option to reload object files... -r
    checking for objdump... objdump
    checking how to recognize dependent libraries... pass_all
    checking for dlltool... no
    checking how to associate runtime and link libraries... printf %s\n
    checking for archiver @FILE support... @
    checking for strip... strip
    checking for ranlib... ranlib
    checking command to parse /bin/nm -B output from gcc object... ok
    checking for sysroot... no
    checking for mt... no
    checking if : is a manifest tool... no
    checking how to run the C preprocessor... gcc -E
    checking for ANSI C header files... yes
    checking for sys/types.h... yes
    checking for sys/stat.h... yes
    checking for stdlib.h... yes
    checking for string.h... yes
    checking for memory.h... yes
    checking for strings.h... yes
    checking for inttypes.h... yes
    checking for stdint.h... yes
    checking for unistd.h... yes
    checking for dlfcn.h... yes
    checking for objdir... .libs
    checking if gcc supports -fno-rtti -fno-exceptions... no
    checking for gcc option to produce PIC... -fPIC -DPIC
    checking if gcc PIC flag -fPIC -DPIC works... yes
    checking if gcc static flag -static works... no
    checking if gcc supports -c -o file.o... yes
    checking if gcc supports -c -o file.o... (cached) yes
    checking whether the gcc linker (/bin/ld -m elf_x86_64) supports shared libraries... yes
    checking whether -lc should be explicitly linked in... no
    checking dynamic linker characteristics... GNU/Linux ld.so
    checking how to hardcode library paths into programs... immediate
    checking for shl_load... no
    checking for shl_load in -ldld... no
    checking for dlopen... no
    checking for dlopen in -ldl... yes
    checking whether a program can dlopen itself... yes
    checking whether a statically linked program can dlopen itself... yes
    checking whether stripping libraries is possible... yes
    checking if libtool supports shared libraries... yes
    checking whether to build shared libraries... yes
    checking whether to build static libraries... yes
    checking if we should install only statically linked executables... no
    checking if -Wl,-rpath should be used to link shared libs in nondefault directories... yes
    checking whether make will build with undefined variables... yes
    checking for production mode... production
    checking for ceil in -lm... yes
    checking for dlopen in -ldl... (cached) yes
    checking for ANSI C header files... (cached) yes
    checking whether time.h and sys/time.h may both be included... yes
    checking sys/resource.h usability... yes
    checking sys/resource.h presence... yes
    checking for sys/resource.h... yes
    checking sys/time.h usability... yes
    checking sys/time.h presence... yes
    checking for sys/time.h... yes
    checking for unistd.h... (cached) yes
    checking sys/ioctl.h usability... yes
    checking sys/ioctl.h presence... yes
    checking for sys/ioctl.h... yes
    checking for sys/stat.h... (cached) yes
    checking sys/socket.h usability... yes
    checking sys/socket.h presence... yes
    checking for sys/socket.h... yes
    checking for sys/types.h... (cached) yes
    checking stddef.h usability... yes
    checking stddef.h presence... yes
    checking for stddef.h... yes
    checking setjmp.h usability... yes
    checking setjmp.h presence... yes
    checking for setjmp.h... yes
    checking features.h usability... yes
    checking features.h presence... yes
    checking for features.h... yes
    checking dirent.h usability... yes
    checking dirent.h presence... yes
    checking for dirent.h... yes
    checking for stdint.h... (cached) yes
    checking mach/mach_time.h usability... no
    checking mach/mach_time.h presence... no
    checking for mach/mach_time.h... no
    checking io.h usability... no
    checking io.h presence... no
    checking for io.h... no
    checking winsock2.h usability... no
    checking winsock2.h presence... no
    checking for winsock2.h... no
    checking sys/timeb.h usability... yes
    checking sys/timeb.h presence... yes
    checking for sys/timeb.h... yes
    checking if libtool needs -no-undefined flag to build shared libraries... no
    checking for _FILE_OFFSET_BITS value needed for large files... no
    checking for off_t... yes
    checking for size_t... yes
    checking for ssize_t... yes
    checking for ptrdiff_t... yes
    checking whether byte ordering is bigendian... no
    checking size of char... 1
    checking size of short... 2
    checking size of int... 4
    checking size of unsigned... 4
    checking size of long... 8
    checking size of long long... 8
    checking size of __int64... 0
    checking size of float... 4
    checking size of double... 8
    checking size of long double... 16
    checking size of int8_t... 1
    checking size of uint8_t... 1
    checking size of int_least8_t... 1
    checking size of uint_least8_t... 1
    checking size of int_fast8_t... 1
    checking size of uint_fast8_t... 1
    checking size of int16_t... 2
    checking size of uint16_t... 2
    checking size of int_least16_t... 2
    checking size of uint_least16_t... 2
    checking size of int_fast16_t... 8
    checking size of uint_fast16_t... 8
    checking size of int32_t... 4
    checking size of uint32_t... 4
    checking size of int_least32_t... 4
    checking size of uint_least32_t... 4
    checking size of int_fast32_t... 8
    checking size of uint_fast32_t... 8
    checking size of int64_t... 8
    checking size of uint64_t... 8
    checking size of int_least64_t... 8
    checking size of uint_least64_t... 8
    checking size of int_fast64_t... 8
    checking size of uint_fast64_t... 8
    checking size of size_t... 8
    checking size of ssize_t... 8
    checking size of ptrdiff_t... 8
    checking size of off_t... 8
    checking if dev_t is scalar... yes
    checking for dmalloc library... suppressed
    checking zlib.h usability... yes
    checking zlib.h presence... yes
    checking for zlib.h... yes
    checking for compress2 in -lz... yes
    checking for compress2... yes
    checking for szlib... suppressed
    checking for thread safe support... no
    checking whether CLOCK_MONOTONIC is declared... yes
    checking for tm_gmtoff in struct tm... yes
    checking for global timezone variable... yes
    checking for st_blocks in struct stat... yes
    checking for _getvideoconfig... no
    checking for gettextinfo... no
    checking for GetConsoleScreenBufferInfo... no
    checking for _scrsize... no
    checking for ioctl... yes
    checking for struct videoconfig... no
    checking for struct text_info... no
    checking for TIOCGWINSZ... yes
    checking for TIOCGETD... yes
    checking for library containing clock_gettime... none required
    checking for alarm... yes
    checking for clock_gettime... yes
    checking for difftime... yes
    checking for fork... yes
    checking for frexpf... yes
    checking for frexpl... yes
    checking for gethostname... yes
    checking for getpwuid... yes
    checking for getrusage... yes
    checking for gettimeofday... yes
    checking for lstat... yes
    checking for rand_r... yes
    checking for random... yes
    checking for setsysinfo... no
    checking for signal... yes
    checking for longjmp... yes
    checking for setjmp... yes
    checking for siglongjmp... yes
    checking for sigsetjmp... no
    checking for sigprocmask... yes
    checking for snprintf... yes
    checking for srandom... yes
    checking for strdup... yes
    checking for symlink... yes
    checking for system... yes
    checking for tmpfile... yes
    checking for asprintf... yes
    checking for vasprintf... yes
    checking for vsnprintf... yes
    checking for waitpid... yes
    checking for an ANSI C-conforming const... yes
    checking if the compiler understands  __inline__... yes
    checking if the compiler understands __inline... yes
    checking if the compiler understands inline... yes
    checking for __attribute__ extension... yes
    checking for __func__ extension... yes
    checking for __FUNCTION__ extension... yes
    checking for C99 designated initialization support... yes
    checking how to print long long... %ld and %lu
    checking Threads support system scope... yes
    checking for debug flags... none
    checking whether function stack tracking is enabled... no
    checking whether metadata trace file code is enabled... no
    checking for API tracing... no
    checking for instrumented library... no
    checking whether to clear file buffers... yes
    checking whether a memory checking tool will be used... no
    checking for parallel support files... skipped
    checking whether O_DIRECT is declared... yes
    checking for posix_memalign... yes
    checking if the direct I/O virtual file driver (VFD) is enabled... no
    checking for custom plugin default path definition... /usr/local/hdf5/lib/plugin
    checking whether exception handling functions is checked during data conversions... yes
    checking whether data accuracy is guaranteed during data conversions... yes
    checking if the machine has window style path name... no
    checking if using special algorithm to convert long double to (unsigned) long values... no
    checking if using special algorithm to convert (unsigned) long to long double values... no
    checking if correctly converting long double to (unsigned) long long values... yes
    checking if correctly converting (unsigned) long long to long double values... yes
    checking additional programs should be built... no
    checking if deprecated public symbols are available... yes
    checking which version of public symbols to use by default... v18
    checking whether to perform strict file format checks... no
    checking whether to have library information embedded in the executables... yes
    checking if alignment restrictions are strictly enforced... no
    configure: creating ./config.lt
    config.lt: creating libtool
    checking that generated files are newer than configure... done
    configure: creating ./config.status
    config.status: creating src/libhdf5.settings
    config.status: creating Makefile
    config.status: creating src/Makefile
    config.status: creating test/Makefile
    config.status: creating test/testcheck_version.sh
    config.status: creating test/testerror.sh
    config.status: creating test/H5srcdir_str.h
    config.status: creating test/testlibinfo.sh
    config.status: creating test/testlinks_env.sh
    config.status: creating test/test_plugin.sh
    config.status: creating testpar/Makefile
    config.status: creating tools/Makefile
    config.status: creating tools/h5dump/Makefile
    config.status: creating tools/h5dump/testh5dump.sh
    config.status: creating tools/h5dump/testh5dumppbits.sh
    config.status: creating tools/h5dump/testh5dumpxml.sh
    config.status: creating tools/h5ls/testh5ls.sh
    config.status: creating tools/h5import/Makefile
    config.status: creating tools/h5import/h5importtestutil.sh
    config.status: creating tools/h5diff/Makefile
    config.status: creating tools/h5diff/testh5diff.sh
    config.status: creating tools/h5diff/testph5diff.sh
    config.status: creating tools/h5jam/Makefile
    config.status: creating tools/h5jam/testh5jam.sh
    config.status: creating tools/h5repack/Makefile
    config.status: creating tools/h5repack/h5repack.sh
    config.status: creating tools/h5repack/h5repack_plugin.sh
    config.status: creating tools/h5ls/Makefile
    config.status: creating tools/h5copy/Makefile
    config.status: creating tools/h5copy/testh5copy.sh
    config.status: creating tools/lib/Makefile
    config.status: creating tools/misc/Makefile
    config.status: creating tools/misc/h5cc
    config.status: creating tools/misc/testh5mkgrp.sh
    config.status: creating tools/misc/testh5repart.sh
    config.status: creating tools/h5stat/testh5stat.sh
    config.status: creating tools/h5stat/Makefile
    config.status: creating tools/perform/Makefile
    config.status: creating examples/Makefile
    config.status: creating examples/run-c-ex.sh
    config.status: creating examples/testh5cc.sh
    config.status: creating c++/Makefile
    config.status: creating c++/src/Makefile
    config.status: creating c++/src/h5c++
    config.status: creating c++/test/Makefile
    config.status: creating c++/test/H5srcdir_str.h
    config.status: creating c++/examples/Makefile
    config.status: creating c++/examples/run-c++-ex.sh
    config.status: creating c++/examples/testh5c++.sh
    config.status: creating fortran/Makefile
    config.status: creating fortran/src/h5fc
    config.status: creating fortran/src/Makefile
    config.status: creating fortran/test/Makefile
    config.status: creating fortran/testpar/Makefile
    config.status: creating fortran/examples/Makefile
    config.status: creating fortran/examples/run-fortran-ex.sh
    config.status: creating fortran/examples/testh5fc.sh
    config.status: creating hl/Makefile
    config.status: creating hl/src/Makefile
    config.status: creating hl/test/Makefile
    config.status: creating hl/test/H5srcdir_str.h
    config.status: creating hl/tools/Makefile
    config.status: creating hl/tools/gif2h5/Makefile
    config.status: creating hl/tools/gif2h5/h52giftest.sh
    config.status: creating hl/examples/Makefile
    config.status: creating hl/examples/run-hlc-ex.sh
    config.status: creating hl/c++/Makefile
    config.status: creating hl/c++/src/Makefile
    config.status: creating hl/c++/test/Makefile
    config.status: creating hl/c++/examples/Makefile
    config.status: creating hl/c++/examples/run-hlc++-ex.sh
    config.status: creating hl/fortran/Makefile
    config.status: creating hl/fortran/src/Makefile
    config.status: creating hl/fortran/test/Makefile
    config.status: creating hl/fortran/examples/Makefile
    config.status: creating hl/fortran/examples/run-hlfortran-ex.sh
    config.status: creating src/H5config.h
    config.status: executing pubconf commands
    creating src/H5pubconf.h
    Post process src/libhdf5.settings
    config.status: executing depfiles commands
    config.status: executing libtool commands
    	    SUMMARY OF THE HDF5 CONFIGURATION
    	    =================================
    
    General Information:
    -------------------
    		   HDF5 Version: 1.8.17
    		  Configured on: Tue May 17 16:54:15 CDT 2016
    		  Configured by: s1a2-472d95bcebf7db-bf066087ecf5@yp-spark-dal09-env5-0024
    		 Configure mode: production
    		    Host system: x86_64-unknown-linux-gnu
    	      Uname information: Linux yp-spark-dal09-env5-0024 3.10.0-229.14.1.el7.x86_64 #1 SMP Tue Sep 15 15:05:51 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
    		       Byte sex: little-endian
    		      Libraries: static, shared
    	     Installation point: /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5
    
    Compiling Options:
    ------------------
                   Compilation Mode: production
                         C Compiler: /bin/gcc ( gcc (GCC) 4.8.3 20140911 )
                             CFLAGS: 
                          H5_CFLAGS: -std=c99 -pedantic -Wall -Wextra -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wcast-qual -Wcast-align -Wwrite-strings -Wconversion -Waggregate-return -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wnested-externs -Winline -Wfloat-equal -Wmissing-format-attribute -Wmissing-noreturn -Wpacked -Wdisabled-optimization -Wformat=2 -Wunreachable-code -Wendif-labels -Wdeclaration-after-statement -Wold-style-definition -Winvalid-pch -Wvariadic-macros -Winit-self -Wmissing-include-dirs -Wswitch-default -Wswitch-enum -Wunused-macros -Wunsafe-loop-optimizations -Wc++-compat -Wstrict-overflow -Wlogical-op -Wlarger-than=2048 -Wvla -Wsync-nand -Wframe-larger-than=16384 -Wpacked-bitfield-compat -Wstrict-overflow=5 -Wjump-misses-init -Wdouble-promotion -Wsuggest-attribute=const -Wtrampolines -Wstack-usage=8192 -Wvector-operation-performance -Wsuggest-attribute=pure -Wsuggest-attribute=noreturn -Wsuggest-attribute=format -O3
                          AM_CFLAGS: 
                           CPPFLAGS: 
                        H5_CPPFLAGS: -D_GNU_SOURCE -D_POSIX_C_SOURCE=200112L   -DNDEBUG -UH5_DEBUG_API
                        AM_CPPFLAGS: 
                   Shared C Library: yes
                   Static C Library: yes
      Statically Linked Executables: no
                            LDFLAGS: 
                         H5_LDFLAGS: 
                         AM_LDFLAGS: 
     	 	Extra libraries: -lz -ldl -lm 
     		       Archiver: ar
     		 	 Ranlib: ranlib
     	      Debugged Packages: 
    		    API Tracing: no
    
    Languages:
    ----------
                            Fortran: no
    
                                C++: no
    
    Features:
    ---------
                      Parallel HDF5: no
                 High Level library: yes
                       Threadsafety: no
                Default API Mapping: v18
     With Deprecated Public Symbols: yes
             I/O filters (external): deflate(zlib)
                                MPE: no
                         Direct VFD: no
                            dmalloc: no
    Clear file buffers before write: yes
               Using memory checker: no
             Function Stack Tracing: no
          Strict File Format Checks: no
       Optimization Instrumentation: no



```python
# Let's make the build and install process as quiet as possible.  Removing all of the H5_CFFLAGS compiler warning settings
!rm config.status.new 2>/dev/null
!cat config.status | sed -n '1h;1!H;${;g;s/"\-std.*O3"/"\-std=c99"/g;p;}' | sed 's/&amp;/\&/g' | sed 's/&lt;/\</g' | sed 's/&gt;/\>/g' > config.status.new
!rm config.status
!mv config.status.new config.status
```


```python
# WARNING:  This cell will take a while ... ~10-12 mins with high CPU on the browser.  
# Your browser may even become unresponsive for a period of time.  Just be patient until the cell execution is complete.  Go grab something to drink.
# It is basically configuring and compiling the native hdf5 libs that are required by H5py 
# NOTE:  You may need to refresh your browser after this cell completes.  Carefully monitor the kernel indicator in the upper right as well.
#        The good news is that this is a one time operation. After the native libs are built, they will be available to all existing and new notebooks within this Spark Instance.
!make -w && make -w install
```

    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
     /bin/sh ./config.status
    config.status: creating src/libhdf5.settings
    config.status: creating Makefile
    config.status: creating src/Makefile
    config.status: creating test/Makefile
    config.status: creating test/testcheck_version.sh
    config.status: creating test/testerror.sh
    config.status: creating test/H5srcdir_str.h
    config.status: creating test/testlibinfo.sh
    config.status: creating test/testlinks_env.sh
    config.status: creating test/test_plugin.sh
    config.status: creating testpar/Makefile
    config.status: creating tools/Makefile
    config.status: creating tools/h5dump/Makefile
    config.status: creating tools/h5dump/testh5dump.sh
    config.status: creating tools/h5dump/testh5dumppbits.sh
    config.status: creating tools/h5dump/testh5dumpxml.sh
    config.status: creating tools/h5ls/testh5ls.sh
    config.status: creating tools/h5import/Makefile
    config.status: creating tools/h5import/h5importtestutil.sh
    config.status: creating tools/h5diff/Makefile
    config.status: creating tools/h5diff/testh5diff.sh
    config.status: creating tools/h5diff/testph5diff.sh
    config.status: creating tools/h5jam/Makefile
    config.status: creating tools/h5jam/testh5jam.sh
    config.status: creating tools/h5repack/Makefile
    config.status: creating tools/h5repack/h5repack.sh
    config.status: creating tools/h5repack/h5repack_plugin.sh
    config.status: creating tools/h5ls/Makefile
    config.status: creating tools/h5copy/Makefile
    config.status: creating tools/h5copy/testh5copy.sh
    config.status: creating tools/lib/Makefile
    config.status: creating tools/misc/Makefile
    config.status: creating tools/misc/h5cc
    config.status: creating tools/misc/testh5mkgrp.sh
    config.status: creating tools/misc/testh5repart.sh
    config.status: creating tools/h5stat/testh5stat.sh
    config.status: creating tools/h5stat/Makefile
    config.status: creating tools/perform/Makefile
    config.status: creating examples/Makefile
    config.status: creating examples/run-c-ex.sh
    config.status: creating examples/testh5cc.sh
    config.status: creating c++/Makefile
    config.status: creating c++/src/Makefile
    config.status: creating c++/src/h5c++
    config.status: creating c++/test/Makefile
    config.status: creating c++/test/H5srcdir_str.h
    config.status: creating c++/examples/Makefile
    config.status: creating c++/examples/run-c++-ex.sh
    config.status: creating c++/examples/testh5c++.sh
    config.status: creating fortran/Makefile
    config.status: creating fortran/src/h5fc
    config.status: creating fortran/src/Makefile
    config.status: creating fortran/test/Makefile
    config.status: creating fortran/testpar/Makefile
    config.status: creating fortran/examples/Makefile
    config.status: creating fortran/examples/run-fortran-ex.sh
    config.status: creating fortran/examples/testh5fc.sh
    config.status: creating hl/Makefile
    config.status: creating hl/src/Makefile
    config.status: creating hl/test/Makefile
    config.status: creating hl/test/H5srcdir_str.h
    config.status: creating hl/tools/Makefile
    config.status: creating hl/tools/gif2h5/Makefile
    config.status: creating hl/tools/gif2h5/h52giftest.sh
    config.status: creating hl/examples/Makefile
    config.status: creating hl/examples/run-hlc-ex.sh
    config.status: creating hl/c++/Makefile
    config.status: creating hl/c++/src/Makefile
    config.status: creating hl/c++/test/Makefile
    config.status: creating hl/c++/examples/Makefile
    config.status: creating hl/c++/examples/run-hlc++-ex.sh
    config.status: creating hl/fortran/Makefile
    config.status: creating hl/fortran/src/Makefile
    config.status: creating hl/fortran/test/Makefile
    config.status: creating hl/fortran/examples/Makefile
    config.status: creating hl/fortran/examples/run-hlfortran-ex.sh
    config.status: creating src/H5config.h
    config.status: src/H5config.h is unchanged
    config.status: executing pubconf commands
    creating src/H5pubconf.h
    src/H5pubconf.h is unchanged
    Post process src/libhdf5.settings
    config.status: executing depfiles commands
    config.status: executing libtool commands
    make: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making all in src
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make  all-am
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
      CC       H5.lo
      CC       H5checksum.lo
      CC       H5dbg.lo
      CC       H5system.lo
      CC       H5timer.lo
      CC       H5trace.lo
      CC       H5A.lo
      CC       H5Abtree2.lo
      CC       H5Adense.lo
      CC       H5Adeprec.lo
      CC       H5Aint.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:[m[K In function '[01m[KH5A_create[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:202:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(NULL == (attr->shared->dt = H5T_copy(type, H5T_COPY_ALL)))
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Apkg.h:39[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:39[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:[m[K In function '[01m[KH5A_write[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:529:13:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                 if((src_id = H5I_register(H5I_DATATYPE, H5T_copy(mem_type, H5T_COPY_ALL), FALSE)) < 0 ||
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Apkg.h:39[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:39[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:[m[K In function '[01m[KH5A_read[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:646:25:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                             (dst_id = H5I_register(H5I_DATATYPE, H5T_copy(mem_type, H5T_COPY_ALL), FALSE)) < 0)
    [01;32m[K                         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Apkg.h:39[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Aint.c:39[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Atest.lo
      CC       H5AC.lo
      CC       H5B.lo
      CC       H5Bcache.lo
      CC       H5Bdbg.lo
      CC       H5B2.lo
      CC       H5B2cache.lo
      CC       H5B2dbg.lo
      CC       H5B2hdr.lo
      CC       H5B2int.lo
      CC       H5B2stat.lo
      CC       H5B2test.lo
      CC       H5C.lo
      CC       H5CS.lo
      CC       H5D.lo
      CC       H5Dbtree.lo
      CC       H5Dchunk.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dchunk.c:[m[K In function '[01m[KH5D__chunk_copy[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dchunk.c:4831:9:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             if(NULL == (dt_mem = H5T_copy(dt_src, H5T_COPY_TRANSIENT)))
    [01;32m[K         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dchunk.c:57[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dchunk.c:4839:9:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             if(NULL == (dt_dst = H5T_copy(dt_src, H5T_COPY_TRANSIENT)))
    [01;32m[K         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dchunk.c:57[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Dcompact.lo
      CC       H5Dcontig.lo
      CC       H5Ddbg.lo
      CC       H5Ddeprec.lo
      CC       H5Defl.lo
      CC       H5Dfill.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:[m[K In function '[01m[KH5D__fill[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:234:13:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                 if((src_id = H5I_register(H5I_DATATYPE, H5T_copy(fill_type, H5T_COPY_ALL), FALSE)) < 0)
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:37[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:237:13:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                 if((dst_id = H5I_register(H5I_DATATYPE, H5T_copy(buf_type, H5T_COPY_ALL), FALSE)) < 0)
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:37[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:[m[K In function '[01m[KH5D__fill_init[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:407:13:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                 if(NULL == (fb_info->mem_type = H5T_copy(dset_type, H5T_COPY_REOPEN)))
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dfill.c:37[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Dint.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dint.c:[m[K In function '[01m[KH5D__init_type[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dint.c:638:9:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             if((dset->shared->type = H5T_copy(type, H5T_COPY_ALL)) == NULL)
    [01;32m[K         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpkg.h:32[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dint.c:30[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Dio.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dio.c:[m[K In function '[01m[KH5D__read[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dio.c:424:5:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[KH5T_patch_vlen_file[m[K' [-Wimplicit-function-declaration]
         H5T_patch_vlen_file(dataset->shared->type, dataset->oloc.file);
    [01;32m[K     ^[m[K
      CC       H5Dlayout.lo
      CC       H5Dmpio.lo
      CC       H5Doh.lo
      CC       H5Dscatgath.lo
      CC       H5Dselect.lo
      CC       H5Dtest.lo
      CC       H5E.lo
      CC       H5Edeprec.lo
      CC       H5Eint.lo
      CC       H5F.lo
      CC       H5Fint.lo
      CC       H5Faccum.lo
      CC       H5Fcwfs.lo
      CC       H5Fdbg.lo
      CC       H5Fefc.lo
      CC       H5Ffake.lo
      CC       H5Fio.lo
      CC       H5Fmount.lo
      CC       H5Fmpi.lo
      CC       H5Fquery.lo
      CC       H5Fsfile.lo
      CC       H5Fsuper.lo
      CC       H5Fsuper_cache.lo
      CC       H5Ftest.lo
      CC       H5FD.lo
      CC       H5FDcore.lo
      CC       H5FDdirect.lo
      CC       H5FDfamily.lo
      CC       H5FDint.lo
      CC       H5FDlog.lo
      CC       H5FDmpi.lo
      CC       H5FDmpio.lo
      CC       H5FDmulti.lo
      CC       H5FDsec2.lo
      CC       H5FDspace.lo
      CC       H5FDstdio.lo
      CC       H5FL.lo
      CC       H5FO.lo
      CC       H5FS.lo
      CC       H5FScache.lo
      CC       H5FSdbg.lo
      CC       H5FSsection.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:[m[K In function '[01m[KH5FS_sect_try_shrink_eoa[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:2338:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5FS_sinfo_lock[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(H5FS_sinfo_lock(f, dxpl_id, fspace, H5AC_WRITE) < 0)
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:207:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5F_t *[m[K' but argument is of type '[01m[Kconst struct H5F_t *[m[K'
     H5FS_sinfo_lock(H5F_t *f, hid_t dxpl_id, H5FS_t *fspace, H5AC_protect_t accmode)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:2338:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 3 of '[01m[KH5FS_sinfo_lock[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(H5FS_sinfo_lock(f, dxpl_id, fspace, H5AC_WRITE) < 0)
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:207:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5FS_t *[m[K' but argument is of type '[01m[Kconst struct H5FS_t *[m[K'
     H5FS_sinfo_lock(H5F_t *f, hid_t dxpl_id, H5FS_t *fspace, H5AC_protect_t accmode)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:2362:7:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5FS_sect_remove_real[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
           if(H5FS_sect_remove_real(fspace, tmp_sect) < 0)
    [01;32m[K       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:859:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5FS_t *[m[K' but argument is of type '[01m[Kconst struct H5FS_t *[m[K'
     H5FS_sect_remove_real(H5FS_t *fspace, H5FS_section_info_t *sect)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:2376:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5FS_sinfo_unlock[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(sinfo_valid && H5FS_sinfo_unlock(f, dxpl_id, fspace, section_removed) < 0)
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:313:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5F_t *[m[K' but argument is of type '[01m[Kconst struct H5F_t *[m[K'
     H5FS_sinfo_unlock(H5F_t *f, hid_t dxpl_id, H5FS_t *fspace, hbool_t modified)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:2376:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 3 of '[01m[KH5FS_sinfo_unlock[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(sinfo_valid && H5FS_sinfo_unlock(f, dxpl_id, fspace, section_removed) < 0)
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FSsection.c:313:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5FS_t *[m[K' but argument is of type '[01m[Kconst struct H5FS_t *[m[K'
     H5FS_sinfo_unlock(H5F_t *f, hid_t dxpl_id, H5FS_t *fspace, hbool_t modified)
    [01;32m[K ^[m[K
      CC       H5FSstat.lo
      CC       H5FStest.lo
      CC       H5G.lo
      CC       H5Gbtree2.lo
      CC       H5Gcache.lo
      CC       H5Gcompact.lo
      CC       H5Gdense.lo
      CC       H5Gdeprec.lo
      CC       H5Gent.lo
      CC       H5Gint.lo
      CC       H5Glink.lo
      CC       H5Gloc.lo
      CC       H5Gname.lo
      CC       H5Gnode.lo
      CC       H5Gobj.lo
      CC       H5Goh.lo
      CC       H5Groot.lo
      CC       H5Gstab.lo
      CC       H5Gtest.lo
      CC       H5Gtraverse.lo
      CC       H5HF.lo
      CC       H5HFbtree2.lo
      CC       H5HFcache.lo
      CC       H5HFdbg.lo
      CC       H5HFdblock.lo
      CC       H5HFdtable.lo
      CC       H5HFhdr.lo
      CC       H5HFhuge.lo
      CC       H5HFiblock.lo
      CC       H5HFiter.lo
      CC       H5HFman.lo
      CC       H5HFsection.lo
      CC       H5HFspace.lo
      CC       H5HFstat.lo
      CC       H5HFtest.lo
      CC       H5HFtiny.lo
      CC       H5HG.lo
      CC       H5HGcache.lo
      CC       H5HGdbg.lo
      CC       H5HGquery.lo
      CC       H5HL.lo
      CC       H5HLcache.lo
      CC       H5HLdbg.lo
      CC       H5HLint.lo
      CC       H5HP.lo
      CC       H5I.lo
      CC       H5Itest.lo
      CC       H5L.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5L.c:[m[K In function '[01m[KH5L_move_cb[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5L.c:2580:13:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5RS_wrap[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
                 dst_name_r = H5RS_wrap(udata->dst_name);
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Gprivate.h:37:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:30[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5L.c:30[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5RSprivate.h:49:20:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kchar *[m[K' but argument is of type '[01m[Kconst char *[m[K'
     H5_DLL H5RS_str_t *H5RS_wrap(char *s);
    [01;32m[K                    ^[m[K
      CC       H5Lexternal.lo
      CC       H5make_libsettings.o
      CCLD     H5make_libsettings
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH`echo  |                  \
    	sed -e 's/-L/:/g' -e 's/ //g'`"                               \
     ./H5make_libsettings > H5lib_settings.c  ||                               \
        (test $HDF5_Make_Ignore && echo "*** Error ignored") ||          \
        (rm -f H5lib_settings.c ; exit 1)
      CC       H5lib_settings.lo
      CC       H5MF.lo
      CC       H5MFaggr.lo
      CC       H5MFdbg.lo
      CC       H5MFsection.lo
      CC       H5MM.lo
      CC       H5MP.lo
      CC       H5MPtest.lo
      CC       H5O.lo
      CC       H5Oainfo.lo
      CC       H5Oalloc.lo
      CC       H5Oattr.lo
      CC       H5Oattribute.lo
      CC       H5Obogus.lo
      CC       H5Obtreek.lo
      CC       H5Ocache.lo
      CC       H5Ochunk.lo
      CC       H5Ocont.lo
      CC       H5Ocopy.lo
      CC       H5Odbg.lo
      CC       H5Odrvinfo.lo
      CC       H5Odtype.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:[m[K In function '[01m[KH5O_dtype_copy[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:1188:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(NULL == (dst = H5T_copy(src, H5T_COPY_ALL)))
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:20[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:[m[K In function '[01m[KH5O_dtype_pre_copy_file[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:1533:9:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             if(NULL == (udata->src_dtype = H5T_copy(dt_src, H5T_COPY_TRANSIENT)))
    [01;32m[K         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Odtype.c:20[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Oefl.lo
      CC       H5Ofill.lo
      CC       H5Oginfo.lo
      CC       H5Olayout.lo
      CC       H5Olinfo.lo
      CC       H5Olink.lo
      CC       H5Omessage.lo
      CC       H5Omtime.lo
      CC       H5Oname.lo
      CC       H5Onull.lo
      CC       H5Opline.lo
      CC       H5Orefcount.lo
      CC       H5Osdspace.lo
      CC       H5Oshared.lo
      CC       H5Ostab.lo
      CC       H5Oshmesg.lo
      CC       H5Otest.lo
      CC       H5Ounknown.lo
      CC       H5P.lo
      CC       H5Pacpl.lo
      CC       H5Pdapl.lo
      CC       H5Pdcpl.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Pdcpl.c:[m[K In function '[01m[KH5P_get_fill_value[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Pdcpl.c:1565:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if((dst_id = H5I_register(H5I_DATATYPE, H5T_copy(type, H5T_COPY_TRANSIENT), FALSE)) < 0)
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Pdcpl.c:39[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Pdeprec.lo
      CC       H5Pdxpl.lo
      CC       H5Pfapl.lo
      CC       H5Pfcpl.lo
      CC       H5Pfmpl.lo
      CC       H5Pgcpl.lo
      CC       H5Pint.lo
      CC       H5Plapl.lo
      CC       H5Plcpl.lo
      CC       H5Pocpl.lo
      CC       H5Pocpypl.lo
      CC       H5Pstrcpl.lo
      CC       H5Ptest.lo
      CC       H5PL.lo
      CC       H5R.lo
      CC       H5Rdeprec.lo
      CC       H5RC.lo
      CC       H5RS.lo
      CC       H5S.lo
      CC       H5Sall.lo
      CC       H5Sdbg.lo
      CC       H5Shyper.lo
      CC       H5Smpio.lo
      CC       H5Snone.lo
      CC       H5Spoint.lo
      CC       H5Sselect.lo
      CC       H5Stest.lo
      CC       H5SL.lo
      CC       H5SM.lo
      CC       H5SMbtree2.lo
      CC       H5SMcache.lo
      CC       H5SMmessage.lo
      CC       H5SMtest.lo
      CC       H5ST.lo
      CC       H5T.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5T.c:[m[K In function '[01m[KH5T_path_find[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5T.c:4485:2:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
      if(NULL == (path->src = H5T_copy(src, H5T_COPY_ALL)))
    [01;32m[K  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5T.c:3115:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5T_copy(H5T_t *old_dt, H5T_copy_t method)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5T.c:4487:9:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             if(NULL == (path->dst = H5T_copy(dst, H5T_COPY_ALL)))
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5T.c:3115:1:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5T_copy(H5T_t *old_dt, H5T_copy_t method)
    [01;32m[K ^[m[K
      CC       H5Tarray.lo
      CC       H5Tbit.lo
      CC       H5Tcommit.lo
      CC       H5Tcompound.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tcompound.c:[m[K In function '[01m[KH5T__insert[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tcompound.c:484:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         parent->shared->u.compnd.memb[idx].type = H5T_copy(member, H5T_COPY_ALL);
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tpkg.h:39:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tcompound.c:38[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Tconv.lo
      CC       H5Tcset.lo
      CC       H5Tdbg.lo
      CC       H5Tdeprec.lo
      CC       H5Tenum.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:[m[K In function '[01m[KH5T__enum_create[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:136:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         ret_value->shared->parent = H5T_copy(parent, H5T_COPY_ALL);
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tpkg.h:39:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:31[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:[m[K In function '[01m[KH5T_enum_nameof[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:437:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(NULL == (copied_dt = H5T_copy(dt, H5T_COPY_ALL)))
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tpkg.h:39:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:31[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:[m[K In function '[01m[KH5T_enum_valueof[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:576:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if (NULL==(copied_dt=H5T_copy(dt, H5T_COPY_ALL)))
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tpkg.h:39:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tenum.c:31[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5Tfields.lo
      CC       H5Tfixed.lo
      CC       H5Tfloat.lo
      CC       H5detect.o
      CCLD     H5detect
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH`echo  |                  \
    	sed -e 's/-L/:/g' -e 's/ //g'`"                               \
     ./H5detect > H5Tinit.c  ||                               \
        (test $HDF5_Make_Ignore && echo "*** Error ignored") ||          \
        (rm -f H5Tinit.c ; exit 1)
      CC       H5Tinit.lo
      CC       H5Tnative.lo
      CC       H5Toffset.lo
      CC       H5Toh.lo
      CC       H5Topaque.lo
      CC       H5Torder.lo
      CC       H5Tpad.lo
      CC       H5Tprecis.lo
      CC       H5Tstrpad.lo
      CC       H5Tvisit.lo
      CC       H5Tvlen.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tvlen.c:[m[K In function '[01m[KH5T__vlen_create[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tvlen.c:168:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 1 of '[01m[KH5T_copy[m[K' discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         if(NULL == (dt->shared->parent = H5T_copy(base, H5T_COPY_ALL)))
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Oprivate.h:41:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dprivate.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tvlen.c:28[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tprivate.h:109:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[Kstruct H5T_t *[m[K' but argument is of type '[01m[Kconst struct H5T_t *[m[K'
     H5_DLL H5T_t *H5T_copy(H5T_t *old_dt, H5T_copy_t method);
    [01;32m[K               ^[m[K
      CC       H5TS.lo
      CC       H5VM.lo
      CC       H5WB.lo
      CC       H5Z.lo
      CC       H5Zdeflate.lo
      CC       H5Zfletcher32.lo
      CC       H5Znbit.lo
      CC       H5Zshuffle.lo
      CC       H5Zszip.lo
      CC       H5Zscaleoffset.lo
      CC       H5Ztrans.lo
      CCLD     libhdf5.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    Making all in test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
      CC       dynlib1.lo
      CCLD     libdynlib1.la
      CC       dynlib2.lo
      CCLD     libdynlib2.la
      CC       dynlib3.lo
      CCLD     libdynlib3.la
      CC       h5test.lo
      CC       testframe.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testframe.c:[m[K In function '[01m[KPrintErrorStackOn[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testframe.c:618:5:[m[K [01;35m[Kwarning: [m[Kpassing argument 2 of '[01m[KH5Eset_auto2[m[K' from incompatible pointer type [enabled by default]
         H5Eset_auto2(H5E_DEFAULT, PrintErrorStackFunc, PrintErrorStackData);
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Eprivate.h:22:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:28[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testframe.c:24[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Epublic.h:174:15:[m[K [01;36m[Knote: [m[Kexpected '[01m[KH5E_auto2_t[m[K' but argument is of type '[01m[Kherr_t (**)(hid_t,  void *)[m[K'
     H5_DLL herr_t H5Eset_auto2(hid_t estack_id, H5E_auto2_t func, void *client_data);
    [01;32m[K               ^[m[K
      CC       cache_common.lo
      CCLD     libh5test.la
      CC       testhdf5.o
      CC       tarray.o
      CC       tattr.o
      CC       tchecksum.o
      CC       tconfig.o
      CC       tfile.o
      CC       tgenprop.o
      CC       th5o.o
      CC       th5s.o
      CC       tcoords.o
      CC       theap.o
      CC       tid.o
      CC       titerate.o
      CC       tmeta.o
      CC       tmisc.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc16[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[Kinitialization makes integer from pointer without a cast [enabled by default]
                             {"1234567", "1234567\0", "12345678", {NULL}};
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[K(near initialization for '[01m[Kwdata[3][0][m[K') [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc17[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[Kinitialization makes integer from pointer without a cast [enabled by default]
                             {"1234567", "1234567\0", "12345678", {NULL}};
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[K(near initialization for '[01m[Kwdata[3][0][m[K') [enabled by default]
      CC       trefer.o
      CC       trefstr.o
      CC       tselect.o
      CC       tskiplist.o
      CC       tsohm.o
      CC       ttime.o
      CC       ttst.o
      CC       tunicode.o
      CC       tvlstr.o
      CC       tvltypes.o
      CCLD     testhdf5
      CC       lheap.o
      CCLD     lheap
      CC       ohdr.o
      CCLD     ohdr
      CC       stab.o
      CCLD     stab
      CC       gheap.o
      CCLD     gheap
      CC       cache.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache.c:[m[K In function '[01m[Kcheck_auto_cache_resize_input_errs[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache.c:25757:9:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[Kresize_configs_are_equal[m[K' [-Wimplicit-function-declaration]
             } else if ( ! resize_configs_are_equal(&test_auto_size_ctl, \
    [01;32m[K         ^[m[K
      CCLD     cache
      CC       cache_api.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache_api.c:[m[K In function '[01m[Kcheck_fapl_mdc_api_calls[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache_api.c:265:2:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[Kresize_configs_are_equal[m[K' [-Wimplicit-function-declaration]
      if ( ! resize_configs_are_equal(&default_auto_size_ctl, \
    [01;32m[K  ^[m[K
      CCLD     cache_api
      CC       pool.o
      CCLD     pool
      CC       accum.o
      CCLD     accum
      CC       hyperslab.o
      CCLD     hyperslab
      CC       istore.o
      CCLD     istore
      CC       bittests.o
      CCLD     bittests
      CC       dt_arith.o
      CCLD     dt_arith
      CC       dtypes.o
      CCLD     dtypes
      CC       dsets.o
      CCLD     dsets
      CC       cmpd_dset.o
      CCLD     cmpd_dset
      CC       filter_fail.o
      CCLD     filter_fail
      CC       extend.o
      CCLD     extend
      CC       external.o
      CCLD     external
      CC       efc.o
      CCLD     efc
      CC       objcopy.o
      CCLD     objcopy
      CC       links.o
      CCLD     links
      CC       unlink.o
      CCLD     unlink
      CC       big.o
      CCLD     big
      CC       mtime.o
      CCLD     mtime
      CC       fillval.o
      CCLD     fillval
      CC       mount.o
      CCLD     mount
      CC       flush1.o
      CCLD     flush1
      CC       flush2.o
      CCLD     flush2
      CC       app_ref.o
      CCLD     app_ref
      CC       enum.o
      CCLD     enum
      CC       set_extent.o
      CCLD     set_extent
      CC       ttsafe.o
      CC       ttsafe_dcreate.o
      CC       ttsafe_error.o
      CC       ttsafe_cancel.o
      CC       ttsafe_acreate.o
      CCLD     ttsafe
      CC       getname.o
      CCLD     getname
      CC       vfd.o
      CCLD     vfd
      CC       ntypes.o
      CCLD     ntypes
      CC       dangle.o
      CCLD     dangle
      CC       dtransform.o
      CCLD     dtransform
      CC       reserved.o
      CCLD     reserved
      CC       cross_read.o
      CCLD     cross_read
      CC       freespace.o
      CCLD     freespace
      CC       mf.o
      CCLD     mf
      CC       btree2.o
      CCLD     btree2
      CC       fheap.o
      CCLD     fheap
      CC       file_image.o
      CCLD     file_image
      CC       unregister.o
      CCLD     unregister
      CC       error_test.o
      CCLD     error_test
      CC       err_compat.o
      CCLD     err_compat
      CC       tcheck_version.o
      CCLD     tcheck_version
      CC       testmeta.o
      CCLD     testmeta
      CC       links_env.o
      CCLD     links_env
      CC       plugin.o
      CCLD     plugin
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    Making all in tools
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making all in lib
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
      CC       h5tools.lo
      CC       h5tools_dump.lo
      CC       h5tools_str.lo
      CC       h5tools_utils.lo
      CC       h5diff.lo
      CC       h5diff_array.lo
      CC       h5diff_attr.lo
      CC       h5diff_dset.lo
      CC       h5diff_util.lo
      CC       h5trav.lo
      CC       h5tools_filters.lo
      CC       h5tools_ref.lo
      CC       h5tools_type.lo
      CC       io_timer.lo
      CCLD     libh5tools.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    Making all in h5diff
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
      CC       h5diff_main.o
      CC       h5diff_common.o
      CCLD     h5diff
      CC       h5diffgentest.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:[m[K In function '[01m[Ktest_comp_vlen_strings[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3702:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp1_buf.str_vlen = comp1_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3703:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp2_buf.str_vlen = comp2_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3704:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp3_buf.str_vlen = comp3_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3705:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp4_buf.str_vlen = comp4_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3706:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp5_buf.str_vlen = comp5_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3707:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp6_buf.str_vlen = comp6_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3708:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp7_buf.str_vlen = comp7_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3709:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp8_buf.str_vlen = comp8_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3710:52:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp9_buf.str_vlen = comp9_buf.str_vlen_repeat = vlen_str_buf;
    [01;32m[K                                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3746:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp1_buf.str_array_vlen[i] = comp1_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3747:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp2_buf.str_array_vlen[i] = comp2_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3748:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp3_buf.str_array_vlen[i] = comp3_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3749:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp4_buf.str_array_vlen[i] = comp4_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3750:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp5_buf.str_array_vlen[i] = comp5_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3751:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp6_buf.str_array_vlen[i] = comp6_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3752:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp7_buf.str_array_vlen[i] = comp7_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3753:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp8_buf.str_array_vlen[i] = comp8_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/tools/h5diff/h5diffgentest.c:3754:73:[m[K [01;35m[Kwarning: [m[Kassignment discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
             comp9_buf.str_array_vlen[i] = comp9_buf.str_vlen_array_again[i] = vlen_str_array_buf[i];
    [01;32m[K                                                                         ^[m[K
      CCLD     h5diffgentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
    Making all in h5ls
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
      CC       h5ls.o
      CCLD     h5ls
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
    Making all in h5dump
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
      CC       h5dump.o
      CC       h5dump_ddl.o
      CC       h5dump_xml.o
      CCLD     h5dump
      CC       h5dumpgentest.o
      CCLD     h5dumpgentest
      CC       binread.o
      CCLD     binread
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    Making all in misc
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
      CC       h5debug.o
      CCLD     h5debug
      CC       h5repart.o
      CCLD     h5repart
      CC       h5mkgrp.o
      CCLD     h5mkgrp
      CC       h5repart_gentest.o
      CCLD     h5repart_gentest
      CC       talign.o
      CCLD     talign
      CC       repart_test.o
      CCLD     repart_test
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    Making all in h5import
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
      CC       h5import.o
      CCLD     h5import
      CC       h5importtest.o
      CCLD     h5importtest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    Making all in h5repack
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
      CC       dynlib_rpk.lo
      CCLD     libdynlibadd.la
      CC       h5repack.o
      CC       h5repack_copy.o
      CC       h5repack_filters.o
      CC       h5repack_opttable.o
      CC       h5repack_parse.o
      CC       h5repack_refs.o
      CC       h5repack_verify.o
      CC       h5repack_main.o
      CCLD     h5repack
      CC       testh5repack_detect_szip.o
      CCLD     testh5repack_detect_szip
      CC       h5repacktst.o
      CCLD     h5repacktst
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    Making all in h5jam
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
      CC       h5jam.o
      CCLD     h5jam
      CC       h5unjam.o
      CCLD     h5unjam
      CC       tellub.o
      CCLD     tellub
      CC       h5jamgentest.o
      CCLD     h5jamgentest
      CC       getub.o
      CCLD     getub
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    Making all in h5copy
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
      CC       h5copy.o
      CCLD     h5copy
      CC       h5copygentest.o
      CCLD     h5copygentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    Making all in h5stat
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
      CC       h5stat.o
      CCLD     h5stat
      CC       h5stat_gentest.o
      CCLD     h5stat_gentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    Making all in perform
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
      CC       sio_perf.o
      CC       sio_engine.o
      CCLD     h5perf_serial
      CC       iopipe.o
      CCLD     iopipe
      CC       chunk.o
      CCLD     chunk
      CC       overhead.o
      CCLD     overhead
      CC       zip_perf.o
      CCLD     zip_perf
      CC       perf_meta.o
      CCLD     perf_meta
      CC       perf.o
      CCLD     perf
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[2]: Nothing to be done for `all-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making all in .
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[1]: Nothing to be done for `all-am'.
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making all in hl
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    Making all in src
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
      CC       H5DO.lo
      CC       H5DS.lo
      CC       H5IM.lo
      CC       H5LT.lo
      CC       H5LTanalyze.lo
      CC       H5LTparse.lo
      CC       H5PT.lo
      CC       H5TB.lo
      CCLD     libhdf5_hl.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
    Making all in test
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
      CC       test_lite.o
      CCLD     test_lite
      CC       test_image.o
      CCLD     test_image
      CC       test_file_image.o
      CCLD     test_file_image
      CC       test_table.o
      CCLD     test_table
      CC       test_ds.o
      CCLD     test_ds
      CC       test_packet.o
      CC       test_packet_vlen.o
      CCLD     test_packet
      CC       test_dset_opt.o
      CCLD     test_dset_opt
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    Making all in tools
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    Making all in gif2h5
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
      CC       gif2hdf.o
      CC       gif2mem.o
      CC       decompress.o
      CC       gifread.o
      CC       writehdf.o
      CCLD     gif2h5
      CC       hdf2gif.o
      CC       hdfgifwr.o
      CCLD     h52gif
      CC       h52gifgentst.o
      CCLD     h52gifgentst
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[3]: Nothing to be done for `all-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[2]: Nothing to be done for `all-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making install in src
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
     /bin/sh ../libtool   --mode=install /bin/install -c   libhdf5.la '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
    libtool: install: /bin/install -c .libs/libhdf5.so.10.2.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so.10.2.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libhdf5.so.10.2.0 libhdf5.so.10 || { rm -f libhdf5.so.10 && ln -s libhdf5.so.10.2.0 libhdf5.so.10; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libhdf5.so.10.2.0 libhdf5.so || { rm -f libhdf5.so && ln -s libhdf5.so.10.2.0 libhdf5.so; }; })
    libtool: install: /bin/install -c .libs/libhdf5.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.la
    libtool: install: /bin/install -c .libs/libhdf5.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.a
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    
    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the `LD_RUN_PATH' environment variable
         during linking
       - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to `/etc/ld.so.conf'
    
    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/hdf5.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5api_adpt.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5overflow.h H5pubconf.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5public.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5version.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Apublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5ACpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Cpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Epubgen.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Epublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Fpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDcore.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDdirect.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDfamily.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDlog.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDmpi.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDmpio.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDmulti.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDsec2.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5FDstdio.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Gpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Ipublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Lpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5MMpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Opublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Ppublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5PLextern.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5PLpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Rpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Spublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Tpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Zpublic.h '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
     /bin/install -c -m 644 libhdf5.settings '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    Making install in test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
     /bin/sh ../libtool   --mode=install /bin/install -c   libdynlib1.la libdynlib2.la libdynlib3.la '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
    libtool: install: /bin/install -c .libs/libdynlib1.so.0.0.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib1.so.0.0.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib1.so.0.0.0 libdynlib1.so.0 || { rm -f libdynlib1.so.0 && ln -s libdynlib1.so.0.0.0 libdynlib1.so.0; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib1.so.0.0.0 libdynlib1.so || { rm -f libdynlib1.so && ln -s libdynlib1.so.0.0.0 libdynlib1.so; }; })
    libtool: install: /bin/install -c .libs/libdynlib1.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib1.la
    libtool: install: /bin/install -c .libs/libdynlib2.so.0.0.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib2.so.0.0.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib2.so.0.0.0 libdynlib2.so.0 || { rm -f libdynlib2.so.0 && ln -s libdynlib2.so.0.0.0 libdynlib2.so.0; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib2.so.0.0.0 libdynlib2.so || { rm -f libdynlib2.so && ln -s libdynlib2.so.0.0.0 libdynlib2.so; }; })
    libtool: install: /bin/install -c .libs/libdynlib2.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib2.la
    libtool: install: /bin/install -c .libs/libdynlib3.so.0.0.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib3.so.0.0.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib3.so.0.0.0 libdynlib3.so.0 || { rm -f libdynlib3.so.0 && ln -s libdynlib3.so.0.0.0 libdynlib3.so.0; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlib3.so.0.0.0 libdynlib3.so || { rm -f libdynlib3.so && ln -s libdynlib3.so.0.0.0 libdynlib3.so; }; })
    libtool: install: /bin/install -c .libs/libdynlib3.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib3.la
    libtool: install: /bin/install -c .libs/libdynlib1.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib1.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib1.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib1.a
    libtool: install: /bin/install -c .libs/libdynlib2.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib2.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib2.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib2.a
    libtool: install: /bin/install -c .libs/libdynlib3.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib3.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib3.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlib3.a
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    
    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the `LD_RUN_PATH' environment variable
         during linking
       - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to `/etc/ld.so.conf'
    
    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
    make  install-exec-hook
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    rm -f /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/*dynlib*
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    Making install in tools
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making install in lib
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    Making install in h5diff
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5diff '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5diff /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5diff
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
    Making install in h5ls
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5ls '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5ls /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5ls
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
    Making install in h5dump
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5dump '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5dump /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5dump
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    Making install in misc
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5debug h5repart h5mkgrp '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5debug /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5debug
    libtool: install: /bin/install -c .libs/h5repart /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5repart
    libtool: install: /bin/install -c .libs/h5mkgrp /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5mkgrp
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
     /bin/install -c h5redeploy '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    Making install in h5import
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5import '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5import /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5import
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    Making install in h5repack
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
     /bin/sh ../../libtool   --mode=install /bin/install -c   libdynlibadd.la '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
    libtool: install: /bin/install -c .libs/libdynlibadd.so.0.0.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlibadd.so.0.0.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlibadd.so.0.0.0 libdynlibadd.so.0 || { rm -f libdynlibadd.so.0 && ln -s libdynlibadd.so.0.0.0 libdynlibadd.so.0; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libdynlibadd.so.0.0.0 libdynlibadd.so || { rm -f libdynlibadd.so && ln -s libdynlibadd.so.0.0.0 libdynlibadd.so; }; })
    libtool: install: /bin/install -c .libs/libdynlibadd.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlibadd.la
    libtool: install: /bin/install -c .libs/libdynlibadd.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlibadd.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlibadd.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libdynlibadd.a
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    
    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the `LD_RUN_PATH' environment variable
         during linking
       - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to `/etc/ld.so.conf'
    
    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5repack '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5repack /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5repack
    make  install-exec-hook
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    rm -f /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/*dynlib*
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    Making install in h5jam
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5jam h5unjam '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5jam /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5jam
    libtool: install: /bin/install -c .libs/h5unjam /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5unjam
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    Making install in h5copy
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5copy '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5copy /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5copy
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    Making install in h5stat
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5stat '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5stat /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5stat
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    Making install in perform
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../libtool   --mode=install /bin/install -c h5perf_serial '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/h5perf_serial /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h5perf_serial
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making install in .
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making install in hl
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    Making install in src
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
     /bin/sh ../../libtool   --mode=install /bin/install -c   libhdf5_hl.la '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib'
    libtool: install: warning: relinking `libhdf5_hl.la'
    libtool: install: (cd /gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src; /bin/sh /gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libtool  --silent --tag CC --mode=relink gcc -std=c99 -version-info 11:0:1 -o libhdf5_hl.la -rpath /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib H5DO.lo H5DS.lo H5IM.lo H5LT.lo H5LTanalyze.lo H5LTparse.lo H5PT.lo H5TB.lo ../../src/libhdf5.la -lz -ldl -lm )
    libtool: install: /bin/install -c .libs/libhdf5_hl.so.10.1.0T /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so.10.1.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libhdf5_hl.so.10.1.0 libhdf5_hl.so.10 || { rm -f libhdf5_hl.so.10 && ln -s libhdf5_hl.so.10.1.0 libhdf5_hl.so.10; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib && { ln -s -f libhdf5_hl.so.10.1.0 libhdf5_hl.so || { rm -f libhdf5_hl.so && ln -s libhdf5_hl.so.10.1.0 libhdf5_hl.so; }; })
    libtool: install: /bin/install -c .libs/libhdf5_hl.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.la
    libtool: install: /bin/install -c .libs/libhdf5_hl.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.a
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    
    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the `LD_RUN_PATH' environment variable
         during linking
       - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to `/etc/ld.so.conf'
    
    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/hdf5_hl.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5DOpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5IMpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5LTpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5TBpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5DSpublic.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/src/H5PTpublic.h '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
    Making install in test
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    Making install in tools
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    Making install in gif2h5
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
      /bin/sh ../../../libtool   --mode=install /bin/install -c gif2h5 h52gif '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin'
    libtool: install: /bin/install -c .libs/gif2h5 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/gif2h5
    libtool: install: /bin/install -c .libs/h52gif /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/bin/h52gif
    make[4]: Nothing to be done for `install-data-am'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[4]: Nothing to be done for `install-exec-am'.
    make[4]: Nothing to be done for `install-data-am'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    for d in examples  hl; do               \
        (cd $d && make  install-examples) || exit 1;           \
    done
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/bin/mkdirs /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_write.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_read.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_extend_write.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_chunk_read.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_crtgrpd.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_subset.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_cmprss.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_rdwt.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_crtgrpar.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_extend.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_crtatt.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_crtgrp.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_crtdat.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_compound.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_group.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_select.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_attribute.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_mount.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_reference.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_drivers.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_extlink.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_elink_unix2win.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_ref2reg.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/h5_shared_mesg.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/ph5example.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c run-c-ex.sh /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/README /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/examples/run-all-ex.sh /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/.
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    for d in examples ; do               \
        (cd $d && make  install-examples) || exit 1;           \
    done
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/examples'
    /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/bin/mkdirs /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_lite1.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_lite2.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_lite3.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ptExampleFL.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_image1.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_image2.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_01.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_02.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_03.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_04.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_05.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_06.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_07.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_08.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_09.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_10.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_11.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_table_12.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/ex_ds1.c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/image24pixel.txt /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/image8.txt /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/pal_rgb.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c run-hlc-ex.sh /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/c/.
    + /bin/install -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/examples/run-hl-ex.sh /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/share/hdf5_examples/hl/.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/examples'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl'
    make: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'


## Housekeeping


```python
# Remove all of configure generated by-products
!rm -rf *
# Remove the extracted source folder
!rm -rf $hdf5Dir/hdf5-1.8.17
# Remove the tar gzip file
!rm $hdf5Dir/hdf5-1.8.17.tar.gz
```


```python
os.environ['HDF5_DIR']= hdf5Dir + "/hdf5"
!pip install h5py --user
```

    Collecting h5py
      Using cached h5py-2.6.0.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): numpy>=1.6.1 in /usr/local/src/bluemix_jupyter_bundle.v3/notebook/lib/python2.7/site-packages (from h5py)
    Requirement already satisfied (use --upgrade to upgrade): six in /usr/local/src/bluemix_jupyter_bundle.v3/notebook/lib/python2.7/site-packages (from h5py)
    Installing collected packages: h5py
      Running setup.py install for h5py ... [?25l- \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | done
    [?25hSuccessfully installed h5py-2.6.0


# Let's work through the h5py Quick Start Guide
[Quick Start Guide](http://docs.h5py.org/en/latest/quick.html)


```python
import h5py
import numpy as np
f = h5py.File("mytestfile1.hdf5", "w")
```


```python
dset = f.create_dataset("mydataset", (100,), dtype='i')
```


```python
dset.shape
```




    (100,)




```python
dset.dtype
```




    dtype('int32')




```python
dset[...] = np.arange(100)
```


```python
dset[0]
```




    0




```python
dset[10]
```




    10




```python
dset[0:100:10]
```




    array([ 0, 10, 20, 30, 40, 50, 60, 70, 80, 90], dtype=int32)




```python
dset.name
```




    u'/mydataset'




```python
f.name
```




    u'/'




```python
grp = f.create_group("subgroup")
```


```python
dset2 = grp.create_dataset("another_dataset", (50,), dtype='f')
```


```python
dset2.name
```




    u'/subgroup/another_dataset'




```python
dset3 = f.create_dataset('subgroup2/dataset_three', (10,), dtype='i')
```


```python
dset3.name
```




    u'/subgroup2/dataset_three'




```python
for name in f:
    print name
```

    mydataset
    subgroup
    subgroup2



```python
dset.attrs['temperature'] = 99.5
dset.attrs['temperature']
```




    99.5




```python
'temperature' in dset.attrs
```




    True




```python

```
