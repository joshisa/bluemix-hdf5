
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
# Uncomment and Run to test modifications/tweaks to this notebook
# !rm -rf $shareDir
# !rm -rf $prefix/.local/lib/python2.7/site-packages/h5py
# !rm -rf $prefix/.local/lib/python2.7/site-packages/h5py-2.6.0-py2.7.egg-info
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

    --2016-05-17 01:36:07--  https://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz
    Resolving www.hdfgroup.org (www.hdfgroup.org)... 50.28.50.143
    Connecting to www.hdfgroup.org (www.hdfgroup.org)|50.28.50.143|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 12304149 (12M) [application/x-gzip]
    Saving to: '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz'
    
    100%[======================================>] 12,304,149  1.43MB/s   in 8.3s   
    
    2016-05-17 01:36:16 (1.41 MB/s) - '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz' saved [12304149/12304149]
    


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
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 .
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 ..
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
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 bin
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 c++
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 config
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users 1034850 May 10 16:24 configure
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  105916 May 10 16:24 configure.ac
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 examples
    drwxr-xr-x  6 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 fortran
    drwxr-xr-x  8 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 hl
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 m4
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 release_docs
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   16384 May 17 01:36 src
    drwxr-xr-x  3 s1a2-472d95bcebf7db-bf066087ecf5 users    8192 May 17 01:36 test
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 testpar
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 17 01:36 tools



```python
# WARNING:  This cell will take a while ... ~10-12 mins with high CPU on the browser.  
# Your browser may even become unresponsive for a period of time.  Just be patient until the cell execution is complete.
# It is basically configuring and compiling the native hdf5 libs that are required by H5py 
!$hdf5SrcDir/configure --prefix=$hdf5Dir/hdf5 && make -w -Wno-cpp && make -w -Wno-cpp install
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
    config.status: src/H5config.h is unchanged
    config.status: executing pubconf commands
    creating src/H5pubconf.h
    src/H5pubconf.h is unchanged
    Post process src/libhdf5.settings
    config.status: executing depfiles commands
    config.status: executing libtool commands
    	    SUMMARY OF THE HDF5 CONFIGURATION
    	    =================================
    
    General Information:
    -------------------
    		   HDF5 Version: 1.8.17
    		  Configured on: Tue May 17 01:37:09 CDT 2016
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
    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making all in src
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make  all-am
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH`echo  |                  \
    	sed -e 's/-L/:/g' -e 's/ //g'`"                               \
     ./H5make_libsettings > H5lib_settings.c  ||                               \
        (test $HDF5_Make_Ignore && echo "*** Error ignored") ||          \
        (rm -f H5lib_settings.c ; exit 1)
      CC       H5lib_settings.lo
    [01m[KH5lib_settings.c:34:6:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[KH5libhdf5_settings[m[K' is 3065 bytes [-Wlarger-than=]
     char H5libhdf5_settings[]=
    [01;32m[K      ^[m[K
      CCLD     libhdf5.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    Making all in test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
      CC       h5test.lo
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_cleanup[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:160:21:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
                         HDsnprintf(temp, sizeof temp, filename, j);
    [01;32m[K                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:160:21:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_fileaccess[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:605:14:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Ksv[m[K' is 7168 bytes [-Wlarger-than=]
             char sv[H5FD_MEM_NTYPES][1024];
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:619:13:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when changing X +- C1 cmp C2 to X cmp C1 +- C2 [-Wstrict-overflow]
                 memb_addr[mt] = (haddr_t)MAX(mt - 1, 0) * (HADDR_MAX / 10);
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_get_file_size[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:986:17:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
                     HDsnprintf(temp, sizeof temp, filename, j);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:986:17:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kprint_func[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:1023:3:[m[K [01;35m[Kwarning: [m[Kfunction might be possible candidate for '[01m[Kgnu_printf[m[K' format attribute [-Wsuggest-attribute=format]
       ret_value = vprintf(format, arglist);
    [01;32m[K   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_make_local_copy[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:1192:11:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kbuf[m[K' is 4096 bytes [-Wlarger-than=]
         char  buf[READ_BUF_SIZE];           /* Buffer for copying data */
    [01;32m[K           ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:28:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_rmprefix[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:526:1:[m[K [01;35m[Kwarning: [m[Kfunction might be candidate for attribute '[01m[Kpure[m[K' [-Wsuggest-attribute=pure]
     h5_rmprefix(const char *filename)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:[m[K In function '[01m[Kh5_fileaccess[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.c:558:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 8464 bytes [-Wstack-usage=]
     h5_fileaccess(void)
    [01;32m[K ^[m[K
      CCLD     libh5test.la
      CC       tarray.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_funcs[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:192:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
         size=H5Tget_precision(type);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:195:21:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
         size=H5Tget_size(type);
    [01;32m[K                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:198:22:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
         size=H5Tget_ebias(type);
    [01;32m[K                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_compound_atomic[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:544:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 wdata[i][j].f = (float)(i * 2.5F + j);
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:544:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:689:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Array data information doesn't match!, wdata[%d][%d].f=%f, rdata[%d][%d].f=%f\n", (int)i, (int)j, wdata[i][j].f, (int)i, (int)j, rdata[i][j].f);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:689:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_compound_array[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:748:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                     wdata[i][j].f[k]=(float)(i * 10.0F + j * 2.5F + k);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:748:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:748:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:932:21:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                         TestErrPrintf("Array data information doesn't match!, wdata[%d][%d].f[%d]=%f, rdata[%d][%d].f[%d]=%f\n",(int)i,(int)j,(int)k,wdata[i][j].f[k],(int)i,(int)j,(int)k,rdata[i][j].f[k]);
    [01;32m[K                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:932:21:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_alloc_custom[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:977:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Ksize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
             *mem_used+=size;
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:977:18:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
             *mem_used+=size;
    [01;32m[K                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_free_custom[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1005:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Ksize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
             *mem_used-=*(size_t *)mem;
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1005:18:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
             *mem_used-=*(size_t *)mem;
    [01;32m[K                  ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:27:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:24[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_vlen_atomic[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1044:40:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong unsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 wdata[i][j].p=HDmalloc((i+j+1)*sizeof(unsigned int));
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5private.h:980:35:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KHDmalloc[m[K'
         #define HDmalloc(Z)    malloc(Z)
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1045:32:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Ksize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 wdata[i][j].len=i+j+1;
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1046:13:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when reducing constant in comparison [-Wstrict-overflow]
                 for(k=0; k<(i+j+1); k++)
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1047:62:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                     ((unsigned int *)wdata[i][j].p)[k]=i*100+j*10+k;
    [01;32m[K                                                              ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:27:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:24[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_vlen_array[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1255:40:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong unsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 wdata[i][j].p=HDmalloc((i+j+1)*(sizeof(unsigned int)*ARRAY1_DIM1));
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5private.h:980:35:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KHDmalloc[m[K'
         #define HDmalloc(Z)    malloc(Z)
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1256:32:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Ksize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 wdata[i][j].len=i+j+1;
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1257:13:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when reducing constant in comparison [-Wstrict-overflow]
                 for(k=0; k<(i+j+1); k++)
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1259:87:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                         ((unsigned int *)wdata[i][j].p)[k*ARRAY1_DIM1+l]=i*1000+j*100+k*10+l;
    [01;32m[K                                                                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K In function '[01m[Ktest_array_bkg[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1537:34:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
        cf[i].b[j] = (float)(100.0F*(i+1) + 0.01F*j);
    [01;32m[K                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1537:4:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
        cf[i].b[j] = (float)(100.0F*(i+1) + 0.01F*j);
    [01;32m[K    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1538:35:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
        cf[i].c[j] = (double)(100.0F*(i+1) + 0.02F*j);
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1538:4:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
        cf[i].c[j] = (double)(100.0F*(i+1) + 0.02F*j);
    [01;32m[K    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1622:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field b data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].b[j],(int)i,(int)j,(float)cfr[i].b[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1622:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1626:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field c data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].c[j],(int)i,(int)j,(float)cfr[i].c[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1626:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1689:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field data doesn't match, fld[%d].b[%d]=%f, fldr[%d].b[%d]=%f\n",(int)i,(int)j,(float)fld[i].b[j],(int)i,(int)j,(float)fldr[i].b[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1689:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1715:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field b data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].b[j],(int)i,(int)j,(float)cfr[i].b[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1715:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1719:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field c data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].c[j],(int)i,(int)j,(float)cfr[i].c[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1719:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1764:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field b data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].b[j],(int)i,(int)j,(float)cfr[i].b[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1764:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1768:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("Field c data doesn't match, cf[%d].b[%d]=%f, cfr[%d].b[%d]=%f\n",(int)i,(int)j,(float)cf[i].c[j],(int)i,(int)j,(float)cfr[i].c[j]);
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:1768:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tarray.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CC       tfile.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:[m[K In function '[01m[Ktest_file_close[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:817:5:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5F_CLOSE_DEFAULT[m[K' not handled in switch [-Wswitch-enum]
         switch(fc_degree) {
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:[m[K In function '[01m[Ktest_obj_count_and_id[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_UNINIT[m[K' not handled in switch [-Wswitch-enum]
                     switch(id_type) {
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_BADID[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_DATATYPE[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_DATASPACE[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_ATTR[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_REFERENCE[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_VFL[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_GENPROP_CLS[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_GENPROP_LST[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_ERROR_CLASS[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_ERROR_MSG[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_ERROR_STACK[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1328:17:[m[K [01;35m[Kwarning: [m[Kenumeration value '[01m[KH5I_NTYPES[m[K' not handled in switch [-Wswitch-enum]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:[m[K In function '[01m[Ktest_file_open_overlap[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1851:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         nobjs = H5Fget_obj_count(fid1, H5F_OBJ_LOCAL|H5F_OBJ_ALL);
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:1872:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         nobjs = H5Fget_obj_count(fid2, H5F_OBJ_ALL);
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:[m[K In function '[01m[Ktest_rw_noupdate[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:2541:13:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
         if(diff > 0.0F) {
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:2574:21:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
             ret = (diff > 0.0F);
    [01;32m[K                     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:26:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tfile.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CC       th5s.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:[m[K In function '[01m[Ktest_h5s_zero_dim[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:543:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kwdata_real[m[K' is 2340 bytes [-Wlarger-than=]
         int                 wdata_real[SPACE1_DIM1][SPACE1_DIM2][SPACE1_DIM3];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:544:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Krdata_real[m[K' is 2340 bytes [-Wlarger-than=]
         int                 rdata_real[SPACE1_DIM1][SPACE1_DIM2][SPACE1_DIM3];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:562:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
                 wdata[i][j] = i + j;
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:564:35:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kshort int[m[K' from '[01m[Kunsigned int[m[K' may alter its value [-Wconversion]
                 wdata_short[i][j] = i + j;
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:571:45:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
                     wdata_real[i][j][k] = i + j + k;
    [01;32m[K                                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:574:89:[m[K [01;35m[Kwarning: [m[Kincrement of enumeration value is invalid in C++ [-Wc++-compat]
         for(alloc_time = H5D_ALLOC_TIME_EARLY; alloc_time <= H5D_ALLOC_TIME_INCR; alloc_time++) {
    [01;32m[K                                                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:[m[K In function '[01m[Ktest_h5s_compound_scalar_read[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1589:9:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
             printf("scalar data different: space4_data.f=%f, read_data4.f=%f\n",space4_data.f,rdata.f);
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1589:9:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1611:9:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kchunk_data_dbl[m[K' is 1200000 bytes [-Wlarger-than=]
     double  chunk_data_dbl[50000][3];
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1612:8:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kchunk_data_flt[m[K' is 600000 bytes [-Wlarger-than=]
     float  chunk_data_flt[50000][3];
    [01;32m[K        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:[m[K In function '[01m[Ktest_h5s_chunk[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1655:47:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 chunk_data_flt[i][j] = (float)((i + 1) * 2.5F - j * 100.3F);
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1655:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 chunk_data_flt[i][j] = (float)((i + 1) * 2.5F - j * 100.3F);
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:31:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:24[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.h:135:41:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
     #define DBL_REL_EQUAL(X,Y,M)    (fabs((Y-X)/X)<M)
    [01;32m[K                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1691:17:[m[K [01;36m[Knote: [m[Kin expansion of macro '[01m[KDBL_REL_EQUAL[m[K'
                 if(!DBL_REL_EQUAL(chunk_data_dbl[i][j], chunk_data_flt[i][j], 0.00001F))
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.h:135:47:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
     #define DBL_REL_EQUAL(X,Y,M)    (fabs((Y-X)/X)<M)
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1691:17:[m[K [01;36m[Knote: [m[Kin expansion of macro '[01m[KDBL_REL_EQUAL[m[K'
                 if(!DBL_REL_EQUAL(chunk_data_dbl[i][j], chunk_data_flt[i][j], 0.00001F))
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:1692:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                     TestErrPrintf("%u: chunk_data_dbl[%d][%d]=%e, chunk_data_flt[%d][%d]=%e\n", (unsigned)__LINE__, i, j, chunk_data_dbl[i][j], i, j, chunk_data_flt[i][j]);
    [01;32m[K                 ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/th5s.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CC       tmisc.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:191:10:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Km13_data[m[K' is 2400 bytes [-Wlarger-than=]
     unsigned m13_data[MISC13_DIM1][MISC13_DIM2];           /* Data to write to dataset */
    [01;32m[K          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:192:10:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Km13_rdata[m[K' is 2400 bytes [-Wlarger-than=]
     unsigned m13_rdata[MISC13_DIM1][MISC13_DIM2];          /* Data read from dataset */
    [01;32m[K          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Kcreate_struct3[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:716:14:[m[K [01;35m[Kwarning: [m[Krequest for implicit conversion from '[01m[Kvoid *[m[K' to '[01m[Kstruct misc5_struct3_hndl *[m[K' not permitted in C++ [-Wc++-compat]
         str3hndl = HDmalloc(sizeof(misc5_struct3_hndl));
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Kcreate_struct2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:759:14:[m[K [01;35m[Kwarning: [m[Krequest for implicit conversion from '[01m[Kvoid *[m[K' to '[01m[Kstruct misc5_struct2_hndl *[m[K' not permitted in C++ [-Wc++-compat]
         str2hndl = HDmalloc(sizeof(misc5_struct2_hndl));
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Kcreate_struct1[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:825:14:[m[K [01;35m[Kwarning: [m[Krequest for implicit conversion from '[01m[Kvoid *[m[K' to '[01m[Kstruct misc5_struct1_hndl *[m[K' not permitted in C++ [-Wc++-compat]
         str1hndl = HDmalloc(sizeof(misc5_struct1_hndl));
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc8[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1246:10:[m[K [01;35m[Kwarning: [m[Krequest for implicit conversion from '[01m[Kvoid *[m[K' to '[01m[Kint *[m[K' not permitted in C++ [-Wc++-compat]
         wdata=HDmalloc(sizeof(int)*MISC8_DIM0*MISC8_DIM1);
    [01;32m[K          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1257:40:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
                 *tdata++=((u*MISC8_DIM1)+v)%13;
    [01;32m[K                                        ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:32:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1313:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1339:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1364:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1408:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1435:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1465:50:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0 * MISC8_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1489:66:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, 4 * MISC8_CHUNK_DIM0 * MISC8_CHUNK_DIM1 * H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                                                  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:1512:47:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong int[m[K' from '[01m[Klong unsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         VERIFY(storage_size, MISC8_DIM0*MISC8_DIM1*H5Tget_size(H5T_NATIVE_INT), "H5Dget_storage_size");
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/testhdf5.h:85:40:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KVERIFY[m[K'
         long __x = (long)_x, __val = (long)_val;          \
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc16[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[Kinitializer-string for array chars is too long for C++ [-Wc++-compat]
                             {"1234567", "1234567\0", "12345678", {NULL}};
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[Kinitializer-string for array chars is too long for C++ [-Wc++-compat]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[Kinitialization makes integer from pointer without a cast [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2725:25:[m[K [01;35m[Kwarning: [m[K(near initialization for '[01m[Kwdata[3][0][m[K') [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc17[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[Kinitializer-string for array chars is too long for C++ [-Wc++-compat]
                             {"1234567", "1234567\0", "12345678", {NULL}};
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[Kinitializer-string for array chars is too long for C++ [-Wc++-compat]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[Kinitialization makes integer from pointer without a cast [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:2804:25:[m[K [01;35m[Kwarning: [m[K(near initialization for '[01m[Kwdata[3][0][m[K') [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc23[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:3882:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kherr_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         status = H5Iget_name(tmp_id, objname, (size_t)MISC23_NAME_BUF_SIZE);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K In function '[01m[Ktest_misc28[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:5038:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             buf[i] = i;
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:5059:34:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             buf[i] = MISC28_SIZE - 1 - i;
    [01;32m[K                                  ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:33:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/tmisc.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     testhdf5
      CC       lheap.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/lheap.c:[m[K In function '[01m[Kmain[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/lheap.c:100:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 buf[j] = '0' + j % 10;
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/lheap.c:133:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 buf[j] = '0' + j % 10;
    [01;32m[K                          ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/lheap.c:23:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/lheap.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     lheap
      CC       ohdr.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/ohdr.c:20:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     ohdr
      CCLD     stab
      CCLD     gheap
      CCLD     cache
      CCLD     cache_api
      CCLD     pool
      CCLD     accum
      CCLD     hyperslab
      CCLD     istore
      CCLD     bittests
      CCLD     dt_arith
      CC       dtypes.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_compound_9[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:1648:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         cmpd_struct wdata = {11, "variable-length string", 22};
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_compound_10[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:1867:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             wdata[i].str[9] += (char)i;
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:1872:43:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             ((char*)(wdata[i].text.p))[len-2] += (char)i;
    [01;32m[K                                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_compound_11[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2111:37:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if(((big_t *)buf_orig)[u].d1!=((little_t *)buf)[u].d1) {
    [01;32m[K                                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2151:37:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if(((big_t *)buf_orig)[u].d1!=((little_t *)buf)[u].d1) {
    [01;32m[K                                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2185:37:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if(((big_t *)buf_orig)[u].d1!=((little_t *)buf)[u].d1) {
    [01;32m[K                                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_compound_13[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2402:19:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
         if(data_out.y != data_in.y) TEST_ERROR
    [01;32m[K                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_compound_14[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2456:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         cmpd_struct_1 wdata1 = {'A', 'B', "variable-length string"};
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:2459:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         cmpd_struct_2 wdata2 = {'C', 'D', "another vlen!", 1, 2, -1, 9001};
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_int_float_except[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:5965:20:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if(*floatp != buf_float[u]) TEST_ERROR
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:5986:20:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if(*floatp != buf2_float[u]) TEST_ERROR
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Kcreate_del_obj_named_test_file[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:6675:12:[m[K [01;35m[Kwarning: [m[Kvariable '[01m[Kstatus[m[K' set but not used [-Wunused-but-set-variable]
         herr_t status;      /* Generic return value */
    [01;32m[K            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K In function '[01m[Ktest_utf_ascii_conv[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:7215:55:[m[K [01;35m[Kwarning: [m[Kcast discards '[01m[K__attribute__((const))[m[K' qualifier from pointer target type [-Wcast-qual]
             status = H5Tconvert(utf8_vtid, ascii_vtid, 1, (void *)utf8_w, NULL, H5P_DEFAULT);
    [01;32m[K                                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:7248:55:[m[K [01;35m[Kwarning: [m[Kcast discards '[01m[K__attribute__((const))[m[K' qualifier from pointer target type [-Wcast-qual]
             status = H5Tconvert(ascii_vtid, utf8_vtid, 1, (void *)ascii_w, NULL, H5P_DEFAULT);
    [01;32m[K                                                       ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:26:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dtypes.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     dtypes
      CC       dsets.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:207:5:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints[m[K' is 80000 bytes [-Wlarger-than=]
     int points[DSET_DIM1][DSET_DIM2], check[DSET_DIM1][DSET_DIM2];
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:207:35:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kcheck[m[K' is 80000 bytes [-Wlarger-than=]
     int points[DSET_DIM1][DSET_DIM2], check[DSET_DIM1][DSET_DIM2];
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:208:8:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints_dbl[m[K' is 160000 bytes [-Wlarger-than=]
     double points_dbl[DSET_DIM1][DSET_DIM2], check_dbl[DSET_DIM1][DSET_DIM2];
    [01;32m[K        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:208:42:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kcheck_dbl[m[K' is 160000 bytes [-Wlarger-than=]
     double points_dbl[DSET_DIM1][DSET_DIM2], check_dbl[DSET_DIM1][DSET_DIM2];
    [01;32m[K                                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_simple_io[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:403:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Krdata[m[K' is 80000 bytes [-Wlarger-than=]
         int                 rdata[DSET_DIM1][DSET_DIM2];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_userblock_offset[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:522:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Krdata[m[K' is 80000 bytes [-Wlarger-than=]
         int                 rdata[DSET_DIM1][DSET_DIM2];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_conv_buffer[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:1030:30:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
      cf->b[j] = (float)(100.0f*(j+1) + 0.01f*j);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:1030:2:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
      cf->b[j] = (float)(100.0f*(j+1) + 0.01f*j);
    [01;32m[K  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:1033:22:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
      cf->c[j] = 100.0f*(j+1) + 0.02f*j;
    [01;32m[K                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:1033:2:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
      cf->c[j] = 100.0f*(j+1) + 0.02f*j;
    [01;32m[K  ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_int[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:2742:28:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                (long long)HDpow(2.0f, (double)(precision - 1))) << offset);
    [01;32m[K                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:2780:28:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 if((new_data[i][j] & mask) != (orig_data[i][j] & mask)) {
    [01;32m[K                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:2780:56:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 if((new_data[i][j] & mask) != (orig_data[i][j] & mask)) {
    [01;32m[K                                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_float[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:2890:33:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 if(!(orig_data[i][j]==orig_data[i][j])) continue;  /* skip if value is NaN */
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:2891:31:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 if(new_data[i][j] != orig_data[i][j]) {
    [01;32m[K                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_double[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3015:33:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 if(!(orig_data[i][j]==orig_data[i][j])) continue;  /* skip if value is NaN */
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3016:31:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 if(new_data[i][j] != orig_data[i][j]) {
    [01;32m[K                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_array[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3109:38:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                          (long long)HDpow(2.0F, (double)precision)) << offset);
    [01;32m[K                                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_compound[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3268:30:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                  (long long)HDpow(2.0F, (double)(precision[0]-1))) << offset[0]);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3270:30:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                  (long long)HDpow(2.0F, (double)(precision[1]-1))) << offset[1]);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3272:30:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                  (long long)HDpow(2.0F, (double)(precision[2]-1))) << offset[2]);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3315:31:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 if((new_data[i][j].i & i_mask) != (orig_data[i][j].i & i_mask) ||
    [01;32m[K                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3315:63:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                 if((new_data[i][j].i & i_mask) != (orig_data[i][j].i & i_mask) ||
    [01;32m[K                                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3316:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
                     (new_data[i][j].c & c_mask) != (orig_data[i][j].c & c_mask) ||
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3316:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3317:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
                     (new_data[i][j].s & s_mask) != (orig_data[i][j].s & s_mask) ||
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3317:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3318:35:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                     (orig_data[i][j].f==orig_data[i][j].f && new_data[i][j].f != orig_data[i][j].f))
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3318:75:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                     (orig_data[i][j].f==orig_data[i][j].f && new_data[i][j].f != orig_data[i][j].f))
    [01;32m[K                                                                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_compound_2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3494:32:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                    (long long)HDpow(2.0F, (double)(precision[0]-1))) << offset[0]);
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3496:32:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                    (long long)HDpow(2.0F, (double)(precision[1]-1))) << offset[1]);
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3498:32:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                    (long long)HDpow(2.0F, (double)(precision[2]-1))) << offset[2]);
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3502:30:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                  (long long)HDpow(2.0F, (double)precision[3])) << offset[3]);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3507:40:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                            (long long)HDpow(2.0F, (double)(precision[4]-1))) << offset[4]);
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3512:42:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                              (long long)HDpow(2.0F, (double)(precision[0]-1))) << offset[0]);
    [01;32m[K                                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3514:42:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                              (long long)HDpow(2.0F, (double)(precision[1]-1))) << offset[1]);
    [01;32m[K                                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3516:42:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong long int[m[K' [-Wbad-function-cast]
                                              (long long)HDpow(2.0F, (double)(precision[2]-1))) << offset[2]);
    [01;32m[K                                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3571:14:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
                  if((new_data[i][j].b[m][n]&b_mask)!=(orig_data[i][j].b[m][n]&b_mask)) {
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3571:14:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3578:40:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                  if((new_data[i][j].d[m][n].i & i_mask)!=(orig_data[i][j].d[m][n].i & i_mask)||
    [01;32m[K                                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3578:78:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
                  if((new_data[i][j].d[m][n].i & i_mask)!=(orig_data[i][j].d[m][n].i & i_mask)||
    [01;32m[K                                                                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3579:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
                     (new_data[i][j].d[m][n].c & c_mask)!=(orig_data[i][j].d[m][n].c & c_mask)||
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3579:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3580:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
                     (new_data[i][j].d[m][n].s & s_mask)!=(orig_data[i][j].d[m][n].s & s_mask)||
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3580:17:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3581:42:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                     (new_data[i][j].d[m][n].f==new_data[i][j].d[m][n].f &&
    [01;32m[K                                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3582:43:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                      new_data[i][j].d[m][n].f != new_data[i][j].d[m][n].f)) {
    [01;32m[K                                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3588:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
             if((new_data[i][j].a.i & i_mask)!=(orig_data[i][j].a.i & i_mask)||
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3588:61:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
             if((new_data[i][j].a.i & i_mask)!=(orig_data[i][j].a.i & i_mask)||
    [01;32m[K                                                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3589:12:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
                (new_data[i][j].a.c & c_mask)!=(orig_data[i][j].a.c & c_mask)||
    [01;32m[K            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3589:12:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kchar[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3590:12:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
                (new_data[i][j].a.s & s_mask)!=(orig_data[i][j].a.s & s_mask)||
    [01;32m[K            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3590:12:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kshort int[m[K' may change the sign of the result [-Wsign-conversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3591:31:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                (new_data[i][j].a.f==new_data[i][j].a.f &&
    [01;32m[K                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3592:32:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 new_data[i][j].a.f != new_data[i][j].a.f)||
    [01;32m[K                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_compound_3[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3712:39:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Klong int[m[K' [-Wbad-function-cast]
             orig_data[i].i = HDrandom() % (long)HDpow(2.0F, 17.0F - 1.0F);
    [01;32m[K                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3712:37:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Klong int[m[K' may alter its value [-Wconversion]
             orig_data[i].i = HDrandom() % (long)HDpow(2.0F, 17.0F - 1.0F);
    [01;32m[K                                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_int_size[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3830:13:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Korig_data[m[K' is 80000 bytes [-Wlarger-than=]
         int     orig_data[DSET_DIM1][DSET_DIM2];
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3882:39:[m[K [01;35m[Kwarning: [m[Kcast from function call of type '[01m[Kdouble[m[K' to non-matching type '[01m[Kint[m[K' [-Wbad-function-cast]
                orig_data[i][j] = rand() % (int)pow((double)2, (double)(precision-1)) << offset;
    [01;32m[K                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_flt_size[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3995:13:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Korig_data[m[K' is 80000 bytes [-Wlarger-than=]
         float   orig_data[DSET_DIM1][DSET_DIM2];
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4080:49:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                orig_data[i][j] = (rand() % 1234567) / 2;
    [01;32m[K                                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_scaleoffset_float[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4464:47:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             orig_data[i][j] = (float)((HDrandom() % 100000) / 1000.0F);
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_scaleoffset_float_2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4595:47:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             orig_data[0][j] = (float)((HDrandom() % 100000) / 1000.0F);
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_scaleoffset_double[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4706:39:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             orig_data[i][j] = (HDrandom() % 10000000) / 10000000.0F;
    [01;32m[K                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_scaleoffset_double_2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4837:39:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             orig_data[0][j] = (HDrandom() % 10000000) / 10000000.0F;
    [01;32m[K                                       ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:26:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_set_local[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.h:135:47:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
     #define DBL_REL_EQUAL(X,Y,M)    (fabs((Y-X)/X)<M)
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:5835:17:[m[K [01;36m[Knote: [m[Kin expansion of macro '[01m[KDBL_REL_EQUAL[m[K'
                 if(!DBL_REL_EQUAL(points_dbl[i][j],check_dbl[i][j],0.00001F)) {
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_deprec[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:6816:12:[m[K [01;35m[Kwarning: [m[Kvariable '[01m[Kstatus[m[K' set but not used [-Wunused-but-set-variable]
         herr_t status;
    [01;32m[K            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_chunk_cache[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:7158:17:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
         w0_2 = w0_1 / 2.0F;
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_big_chunks_bypass_cache[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:7339:44:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Krdata1[m[K' is 4000 bytes [-Wlarger-than=]
         static int  wdata[BYPASS_CHUNK_DIM/2], rdata1[BYPASS_DIM],
    [01;32m[K                                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_chunk_expand[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:7583:93:[m[K [01;35m[Kwarning: [m[Kincrement of enumeration value is invalid in C++ [-Wc++-compat]
             for(alloc_time = H5D_ALLOC_TIME_EARLY; alloc_time <= H5D_ALLOC_TIME_INCR; alloc_time++) {
    [01;32m[K                                                                                             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:27:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_simple_io[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:394:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 81136 bytes [-Wstack-usage=]
     test_simple_io(const char *env_h5_drvr, hid_t fapl)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:496:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 81052 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_int_size[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3825:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 80112 bytes [-Wstack-usage=]
     test_nbit_int_size(hid_t file)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3971:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 80040 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_nbit_flt_size[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:3990:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 80112 bytes [-Wstack-usage=]
     test_nbit_flt_size(hid_t file)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:4168:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 80032 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Ktest_userblock_offset[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:514:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 81088 bytes [-Wstack-usage=]
     test_userblock_offset(const char *env_h5_drvr, hid_t fapl)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:586:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 81040 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:[m[K In function '[01m[Kfilter_bogus2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/dsets.c:1360:18:[m[K [01;35m[Kwarning: [m[Kcannot optimize loop, the loop counter may overflow [-Wunsafe-loop-optimizations]
                 while(buf_left>0) {
    [01;32m[K                  ^[m[K
      CCLD     dsets
      CCLD     cmpd_dset
      CC       filter_fail.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/filter_fail.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     filter_fail
      CCLD     extend
      CCLD     external
      CCLD     efc
      CC       objcopy.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Kcompare_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:1067:24:[m[K [01;35m[Kwarning: [m[Kcast discards '[01m[K__attribute__((const))[m[K' qualifier from pointer target type [-Wcast-qual]
                 ref_buf1 = (const hdset_reg_ref_t *)buf1;
    [01;32m[K                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:1068:24:[m[K [01;35m[Kwarning: [m[Kcast discards '[01m[K__attribute__((const))[m[K' qualifier from pointer target type [-Wcast-qual]
                 ref_buf2 = (const hdset_reg_ref_t *)buf2;
    [01;32m[K                        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_compound[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2318:30:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             buf[i].d = 1.0F / (i + 1);
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_chunked[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2450:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             buf1d[i] = (float)(i / 2.0F);
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2452:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 buf2d[i][j] = (float)(i + (j / 100.0F));
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2452:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_chunked_sparse[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2793:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
             buf1d[i] = (float)(i / 10.0F);
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2795:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 buf2d[i][j] = (float)(i + (j / 100.0F));
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:2795:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_compact[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:3117:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
                 buf[i][j] = (float)(i+j/100.0F);
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:3117:13:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kint[m[K' may alter its value [-Wconversion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_contig_cmpd_vl[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7597:22:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
             buf[i].a = i * (i - 1);
    [01;32m[K                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7602:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kunsigned int[m[K' may alter its value [-Wconversion]
             buf[i].c = 1.0F / (i + 1.0F);
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_chunked_cmpd_vl[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7736:22:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
             buf[i].a = i * (i - 1);
    [01;32m[K                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7741:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kunsigned int[m[K' may alter its value [-Wconversion]
             buf[i].c = 1.0F / (i + 1.0F);
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Ktest_copy_dataset_compact_cmpd_vl[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7881:22:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
             buf[i].a = i * (i - 1);
    [01;32m[K                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:7886:9:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kfloat[m[K' from '[01m[Kunsigned int[m[K' may alter its value [-Wconversion]
             buf[i].c = 1.0F / (i + 1.0F);
    [01;32m[K         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:[m[K In function '[01m[Kaddr_lookup.isra.0[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/objcopy.c:201:1:[m[K [01;35m[Kwarning: [m[Kfunction might be candidate for attribute '[01m[Kpure[m[K' if it is known to return normally [-Wsuggest-attribute=pure]
     addr_lookup(H5O_info_t *oi)
    [01;32m[K ^[m[K
      CCLD     objcopy
      CC       links.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_link_dangling[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:2698:36:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khid_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
             status = H5Lget_name_by_idx(rid, "no_file", H5_INDEX_NAME, H5_ITER_INC, 0, NULL, 0, H5P_DEFAULT);
    [01;32m[K                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_set_elink_fapl1[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:3500:17:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Ksv[m[K' is 3500 bytes [-Wlarger-than=]
         char        sv[H5FD_MEM_NTYPES][500];
    [01;32m[K                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_set_elink_fapl2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:3699:10:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints[m[K' is 6400 bytes [-Wlarger-than=]
         int  points[NUM40][NUM40];
    [01;32m[K          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Klink_filters[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:9802:48:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
                     > ((double)filesize_unfiltered * FILTER_FILESIZE_MAX_FRACTION))
    [01;32m[K                                                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Klink_info_by_idx[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:11058:41:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kherr_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
                     ret = H5Lget_name_by_idx(group_id, ".", H5_INDEX_CRT_ORDER, H5_ITER_INC, (hsize_t)0, tmpname, (size_t)NAME_BUF_SIZE, H5P_DEFAULT);
    [01;32m[K                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:11100:41:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kherr_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
                     ret = H5Lget_name_by_idx(group_id, ".", H5_INDEX_CRT_ORDER, H5_ITER_INC, (hsize_t)u, tmpname, (size_t)NAME_BUF_SIZE, H5P_DEFAULT);
    [01;32m[K                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:11142:41:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kherr_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
                     ret = H5Lget_name_by_idx(group_id, ".", H5_INDEX_CRT_ORDER, H5_ITER_INC, (hsize_t)u, tmpname, (size_t)NAME_BUF_SIZE, H5P_DEFAULT);
    [01;32m[K                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Klink_info_by_idx_old[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:11310:37:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kherr_t[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
                 ret = H5Lget_name_by_idx(group_id, ".", H5_INDEX_CRT_ORDER, H5_ITER_INC, (hsize_t)u, tmpname, (size_t)NAME_BUF_SIZE, H5P_DEFAULT);
    [01;32m[K                                     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Klink_iterate_check[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12070:70:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint64_t[m[K' from '[01m[Khsize_t[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? skip : ((max_links - 1) - skip);
    [01;32m[K                                                                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12070:5:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint64_t[m[K' from '[01m[Khsize_t[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? skip : ((max_links - 1) - skip);
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12098:45:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = max_links / 2;
    [01;32m[K                                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12098:33:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = max_links / 2;
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12102:5:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? (unsigned)gskip : ((max_links - 1) - gskip);
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12143:33:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = 0;
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Klink_iterate_old_check[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12500:33:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = 0;
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12520:70:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint64_t[m[K' from '[01m[Khsize_t[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? skip : ((max_links - 1) - skip);
    [01;32m[K                                                                      ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12520:5:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint64_t[m[K' from '[01m[Khsize_t[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? skip : ((max_links - 1) - skip);
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12548:45:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = max_links / 2;
    [01;32m[K                                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12548:33:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = max_links / 2;
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12552:5:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->curr = order != H5_ITER_DEC ? (unsigned)gskip : ((max_links - 1) - gskip);
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:12593:33:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         iter_info->nskipped = gskip = 0;
    [01;32m[K                                 ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:31:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_set_elink_fapl2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:3689:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 10672 bytes [-Wstack-usage=]
     external_set_elink_fapl2(hid_t fapl, hbool_t new_format)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_set_elink_fapl1[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:3485:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 8944 bytes [-Wstack-usage=]
     external_set_elink_fapl1(hid_t fapl, hbool_t new_format)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:[m[K In function '[01m[Kexternal_symlink[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/links.c:6339:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 11392 bytes [-Wstack-usage=]
     external_symlink(const char *env_h5_drvr, hid_t fapl, hbool_t new_format)
    [01;32m[K ^[m[K
      CCLD     links
      CCLD     unlink
      CCLD     big
      CC       mtime.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/mtime.c:[m[K In function '[01m[Kmain[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/mtime.c:117:50:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
         } else if(HDfabs(HDdifftime(now, oi1.ctime)) > 60.0F) {
    [01;32m[K                                                  ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/mtime.c:26:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/mtime.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     mtime
      CC       fillval.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K In function '[01m[Ktest_create[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:626:19:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if( rd_c.a!=0 || rd_c.y != fill_ctype.y || rd_c.x != 0 || rd_c.z != '\0') {
    [01;32m[K                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:626:33:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
             if( rd_c.a!=0 || rd_c.y != fill_ctype.y || rd_c.x != 0 || rd_c.z != '\0') {
    [01;32m[K                                 ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:630:19:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                       rd_c.a, rd_c.y, rd_c.x, rd_c.z);
    [01;32m[K                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:699:15:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
         if(rd_c.a != 0 || rd_c.y != fill_ctype.y || rd_c.x != 0 || rd_c.z!='\0') {
    [01;32m[K               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:699:30:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
         if(rd_c.a != 0 || rd_c.y != fill_ctype.y || rd_c.x != 0 || rd_c.z!='\0') {
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:703:3:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
       rd_c.a, rd_c.y, rd_c.x, rd_c.z);
    [01;32m[K   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K In function '[01m[Ktest_rdwr_cases[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:792:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % cur_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:814:57:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                 if(fill_time!=H5D_FILL_TIME_NEVER && (rd_c.a!=fill_c.a ||
    [01;32m[K                                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:815:29:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
       rd_c.x!=fill_c.x || rd_c.y!=fill_c.y ||
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:824:4:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
        fill_c.a, fill_c.x, fill_c.y, fill_c.z);
    [01;32m[K    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:824:4:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:848:13:[m[K [01;35m[Kwarning: [m[Krequest for implicit conversion from '[01m[Kvoid *[m[K' to '[01m[Kint *[m[K' not permitted in C++ [-Wc++-compat]
             buf = HDmalloc((size_t)(nelmts * sizeof(int)));
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:882:31:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                     if(buf_c[u].a != fill_c.a || buf_c[u].x != fill_c.x ||
    [01;32m[K                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:883:36:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                             buf_c[u].y != fill_c.y || buf_c[u].z != fill_c.z) {
    [01;32m[K                                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:892:29:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                                 fill_c.a, fill_c.x, fill_c.y, fill_c.z);
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:892:29:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:941:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % cur_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:996:13:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
       if( rd_c.a!=should_be_c.a || rd_c.x!=should_be_c.x ||
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:997:13:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
           rd_c.y!=should_be_c.y || rd_c.z!=should_be_c.z)  {
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1006:14:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                  should_be_c.x,should_be_c.y,should_be_c.z);
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1006:14:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1015:27:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                     if( rd_c.a!=should_be_c.a || rd_c.x!=should_be_c.x ||
    [01;32m[K                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1016:27:[m[K [01;35m[Kwarning: [m[Kcomparing floating point with == or != is unsafe [-Wfloat-equal]
                         rd_c.y!=should_be_c.y || rd_c.z!=should_be_c.z)  {
    [01;32m[K                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1025:28:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
                                should_be_c.x,should_be_c.y,should_be_c.z);
    [01;32m[K                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1025:28:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' when passing argument to function [-Wdouble-promotion]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K In function '[01m[Ktest_extend_cases[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1443:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp_vl_datatype val_rd_c, init_val_c = {87, "baz", "mumble", 129};
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1443:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1498:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % start_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1551:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % start_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1589:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % extend_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1628:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % max_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1669:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % extend_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1762:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Khsize_t[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
          hs_offset[j] = rand() % extend_size[j];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K In function '[01m[Ktest_extend[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1845:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
         comp_vl_datatype fillval_c = {32, "foo", "bar", 64};         /* Fill value for compound+vl datatype tests */
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:1845:5:[m[K [01;35m[Kwarning: [m[Kinitialization discards '[01m[Kconst[m[K' qualifier from pointer target type [enabled by default]
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:23:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:[m[K In function '[01m[Ktest_rdwr_cases[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:886:30:[m[K [01;35m[Kwarning: [m[K'[01m[Kfill_c.a[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
                         HDfprintf(stdout,"    Elmt={%Hu, %Hu, %Hu, %Hu, %Hu}, read: %f, %d, %f, %c"
    [01;32m[K                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:886:30:[m[K [01;35m[Kwarning: [m[K'[01m[Kfill_c.x[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:886:30:[m[K [01;35m[Kwarning: [m[K'[01m[Kfill_c.y[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/fillval.c:886:30:[m[K [01;35m[Kwarning: [m[K'[01m[Kfill_c.z[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
      CCLD     fillval
      CCLD     mount
      CCLD     flush1
      CCLD     flush2
      CCLD     app_ref
      CCLD     enum
      CCLD     set_extent
      CCLD     ttsafe
      CCLD     getname
      CCLD     vfd
      CCLD     ntypes
      CCLD     dangle
      CCLD     dtransform
      CCLD     reserved
      CC       cross_read.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:24:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:[m[K In function '[01m[Kcheck_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.h:135:47:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
     #define DBL_REL_EQUAL(X,Y,M)    (fabs((Y-X)/X)<M)
    [01;32m[K                                               ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:129:8:[m[K [01;36m[Knote: [m[Kin expansion of macro '[01m[KDBL_REL_EQUAL[m[K'
       if (!DBL_REL_EQUAL(data_out[j][i], data_in[j][i], 0.001F)) {
    [01;32m[K        ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:[m[K In function '[01m[Kopen_dataset[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:210:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:213:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME1, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:216:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME2, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:219:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME3, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:222:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME4, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:225:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME5, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:228:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME6, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:231:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME7, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:234:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME8, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:237:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME9, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:240:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME10, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:243:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME11, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:246:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME12, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:249:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME13, file, FALSE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:252:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME14, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:255:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME15, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:259:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME16, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:267:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME17, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:290:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME20, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:293:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME21, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:296:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME22, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:299:26:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += check_data(DATASETNAME23, file, TRUE);
    [01;32m[K                          ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:309:5:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kunsigned int[m[K' may change the sign of the result [-Wsign-conversion]
         return nerrors;
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:[m[K In function '[01m[Kmain[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:335:28:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += open_dataset(filename);
    [01;32m[K                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:339:28:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kunsigned int[m[K' from '[01m[Kint[m[K' may change the sign of the result [-Wsign-conversion]
         nerrors += open_dataset(filename);
    [01;32m[K                            ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cross_read.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     cross_read
      CCLD     freespace
      CCLD     mf
      CCLD     btree2
      CCLD     fheap
      CC       file_image.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:[m[K In function '[01m[Ktest_get_file_image[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:768:13:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
                 HDsnprintf(member_file_name, 1024, file_name, i);
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:768:13:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:796:13:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
                 HDsnprintf(member_file_name, 1024, file_name, i);
    [01;32m[K             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:796:13:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:25:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/file_image.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     file_image
      CCLD     unregister
      CC       error_test.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:43:5:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kipoints2[m[K' is 80000 bytes [-Wlarger-than=]
     int ipoints2[DIM0][DIM1], icheck2[DIM0][DIM1];
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:43:27:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kicheck2[m[K' is 80000 bytes [-Wlarger-than=]
     int ipoints2[DIM0][DIM1], icheck2[DIM0][DIM1];
    [01;32m[K                           ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:[m[K In function '[01m[Kerror_stack[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:276:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         if((err_num = H5Eget_num(H5E_DEFAULT)) < 0)
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:285:29:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         if((err_num = H5Eget_num(ERR_STACK)) == 0) {
    [01;32m[K                             ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:[m[K In function '[01m[Ktest_long_desc[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:357:28:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kchar[m[K' from '[01m[Ksize_t[m[K' may alter its value [-Wconversion]
             long_desc[u] = 'A' + (u % 26);
    [01;32m[K                            ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:368:5:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
         HDsnprintf(full_desc, (size_t)(LONG_DESC_SIZE + 128), format, long_desc);
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:368:5:[m[K [01;35m[Kwarning: [m[Kformat not a string literal, argument types not checked [-Wformat-nonliteral]
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:[m[K In function '[01m[Ktest_create[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:497:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(estack_id);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:504:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(estack_id);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:511:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(estack_id);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:[m[K In function '[01m[Ktest_copy[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:549:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(H5E_DEFAULT);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:556:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(estack_id);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:560:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(H5E_DEFAULT);
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:567:25:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Kint[m[K' from '[01m[Kssize_t[m[K' may alter its value [-Wconversion]
         err_num = H5Eget_num(H5E_DEFAULT);
    [01;32m[K                         ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:23:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/error_test.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     error_test
      CCLD     err_compat
      CCLD     tcheck_version
      CCLD     testmeta
      CCLD     links_env
      CC       plugin.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:65:5:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints_deflate[m[K' is 80000 bytes [-Wlarger-than=]
     int points_deflate[DSET_DIM1][DSET_DIM2], 
    [01;32m[K     ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:66:9:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints_dynlib1[m[K' is 80000 bytes [-Wlarger-than=]
             points_dynlib1[DSET_DIM1][DSET_DIM2],
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:67:9:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints_dynlib2[m[K' is 80000 bytes [-Wlarger-than=]
             points_dynlib2[DSET_DIM1][DSET_DIM2],
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:68:9:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints_bzip2[m[K' is 80000 bytes [-Wlarger-than=]
             points_bzip2[DSET_DIM1][DSET_DIM2];
    [01;32m[K         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_filter_internal[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:95:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kpoints[m[K' is 80000 bytes [-Wlarger-than=]
         int                 points[DSET_DIM1][DSET_DIM2], check[DSET_DIM1][DSET_DIM2];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:95:55:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kcheck[m[K' is 80000 bytes [-Wlarger-than=]
         int                 points[DSET_DIM1][DSET_DIM2], check[DSET_DIM1][DSET_DIM2];
    [01;32m[K                                                       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_read_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:427:25:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kcheck[m[K' is 80000 bytes [-Wlarger-than=]
         int                 check[DSET_DIM1][DSET_DIM2];
    [01;32m[K                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_noread_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:541:16:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kcheck[m[K' is 80000 bytes [-Wlarger-than=]
         int        check[DSET_DIM1][DSET_DIM2];
    [01;32m[K                ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_noread_with_filters[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:577:5:[m[K [01;35m[Kwarning: [m[Knegative integer implicitly converted to unsigned type [-Wsign-conversion]
         plugin_state = plugin_state & ~H5PL_FILTER_PLUGIN;
    [01;32m[K     ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:24:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_read_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:425:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 80032 bytes [-Wstack-usage=]
     test_read_data(hid_t dataset, int *origin_data)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:456:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 80000 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_filter_internal[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:85:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 160208 bytes [-Wstack-usage=]
     test_filter_internal(hid_t fid, const char *name, hid_t dcpl)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:325:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 160128 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:[m[K In function '[01m[Ktest_noread_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:539:1:[m[K [01;35m[Kwarning: [m[Kstack usage is 80064 bytes [-Wstack-usage=]
     test_noread_data(hid_t dataset)
    [01;32m[K ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/plugin.c:556:1:[m[K [01;35m[Kwarning: [m[Kthe frame size of 80048 bytes is larger than 16384 bytes [-Wframe-larger-than=]
     }
    [01;32m[K ^[m[K
      CCLD     plugin
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    Making all in tools
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making all in lib
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
    Making all in h5diff
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
      CCLD     h5diff
      CCLD     h5diffgentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5diff'
    Making all in h5ls
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
      CCLD     h5ls
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5ls'
    Making all in h5dump
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
      CCLD     h5dump
      CCLD     h5dumpgentest
      CCLD     binread
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    Making all in misc
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
      CCLD     h5debug
      CCLD     h5repart
      CCLD     h5mkgrp
      CCLD     h5repart_gentest
      CCLD     talign
      CCLD     repart_test
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    Making all in h5import
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
      CCLD     h5import
      CCLD     h5importtest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    Making all in h5repack
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
      CCLD     h5repack
      CCLD     testh5repack_detect_szip
      CCLD     h5repacktst
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    Making all in h5jam
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
      CCLD     h5jam
      CCLD     h5unjam
      CCLD     tellub
      CCLD     h5jamgentest
      CCLD     getub
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    Making all in h5copy
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
      CCLD     h5copy
      CCLD     h5copygentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    Making all in h5stat
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
      CCLD     h5stat
      CCLD     h5stat_gentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    Making all in perform
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
      CCLD     h5perf_serial
      CCLD     iopipe
      CCLD     chunk
      CCLD     overhead
      CCLD     zip_perf
      CCLD     perf_meta
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
      CCLD     libhdf5_hl.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src'
    Making all in test
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
      CC       test_lite.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_lite.c:19:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_lite.c:[m[K In function '[01m[Kmain[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_lite.c:1313:7:[m[K [01;35m[Kwarning: [m[K'[01m[Kdt_str[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
         if(dt_str)
    [01;32m[K       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_lite.c:1199:13:[m[K [01;36m[Knote: [m[K'[01m[Kdt_str[m[K' was declared here
         char*   dt_str;
    [01;32m[K             ^[m[K
      CCLD     test_lite
      CC       test_image.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_image.c:21:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_image.c:[m[K In function '[01m[Ktest_generate[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_image.c:754:7:[m[K [01;35m[Kwarning: [m[K'[01m[Kdata[m[K' may be used uninitialized in this function [-Wmaybe-uninitialized]
         if(data)
    [01;32m[K       ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_image.c:[m[K In function '[01m[Kread_data[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_image.c:844:5:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when simplifying conditional to constant [-Wstrict-overflow]
         for(i = 0; i < n_elements; i++) {
    [01;32m[K     ^[m[K
      CCLD     test_image
      CCLD     test_file_image
      CC       test_table.o
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_table.c:19:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     test_table
      CC       test_ds.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:[m[K In function '[01m[Kcreate_long_dataset[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:392:13:[m[K [01;35m[Kwarning: [m[Ksize of '[01m[Kbuf[m[K' is 2304 bytes [-Wlarger-than=]
         long    buf[DIM1_SIZE*DIM2_SIZE*DIM3_SIZE*DIM4_SIZE];
    [01;32m[K             ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/h5test.h:26:0[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/h5hltest.h:27[m[K,
                     from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:18[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:[m[K In function '[01m[Ktest_cmp_scalename[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:1108:46:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong unsigned int[m[K' from '[01m[Kssize_t[m[K' may change the sign of the result [-Wsign-conversion]
              name_out = (char*)HDmalloc((name_len+1) * sizeof (char));
    [01;32m[K                                              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5private.h:980:35:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KHDmalloc[m[K'
         #define HDmalloc(Z)    malloc(Z)
    [01;32m[K                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:[m[K In function '[01m[Ktest_simple[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:3129:41:[m[K [01;35m[Kwarning: [m[Kconversion to '[01m[Klong unsigned int[m[K' from '[01m[Kssize_t[m[K' may change the sign of the result [-Wsign-conversion]
         name_out = (char*)HDmalloc((name_len+1) * sizeof (char));
    [01;32m[K                                         ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5private.h:980:35:[m[K [01;36m[Knote: [m[Kin definition of macro '[01m[KHDmalloc[m[K'
         #define HDmalloc(Z)    malloc(Z)
    [01;32m[K                                   ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:19:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_ds.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
      CCLD     test_ds
      CCLD     test_packet
      CC       test_dset_opt.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:[m[K In function '[01m[Ktest_direct_chunk_write[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:44:51:[m[K [01;35m[Kwarning: [m[Kimplicit conversion from '[01m[Kfloat[m[K' to '[01m[Kdouble[m[K' to match other operand of binary expression [-Wdouble-promotion]
     #define DEFLATE_SIZE_ADJUST(s) (ceil(((double)(s))*1.001F)+12)
    [01;32m[K                                                   ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:116:40:[m[K [01;36m[Knote: [m[Kin expansion of macro '[01m[KDEFLATE_SIZE_ADJUST[m[K'
         uLongf      z_dst_nbytes = (uLongf)DEFLATE_SIZE_ADJUST(buf_size);
    [01;32m[K                                        ^[m[K
    In file included from [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:19:0[m[K:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:[m[K At top level:
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:35:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir_filename[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir_filename(const char *filename)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/H5srcdir.h:53:20:[m[K [01;35m[Kwarning: [m[K'[01m[KH5_get_srcdir[m[K' defined but not used [-Wunused-function]
     static const char *H5_get_srcdir(void)
    [01;32m[K                    ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:[m[K In function '[01m[Kfilter_bogus1[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:508:14:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when changing X +- C1 cmp C2 to X cmp C1 +- C2 [-Wstrict-overflow]
             while(buf_left>0) {
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:515:14:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when changing X +- C1 cmp C2 to X cmp C1 +- C2 [-Wstrict-overflow]
             while(buf_left>0) {
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:[m[K In function '[01m[Kfilter_bogus2[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:545:14:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when changing X +- C1 cmp C2 to X cmp C1 +- C2 [-Wstrict-overflow]
             while(buf_left>0) {
    [01;32m[K              ^[m[K
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/hl/test/test_dset_opt.c:552:14:[m[K [01;35m[Kwarning: [m[Kassuming signed overflow does not occur when changing X +- C1 cmp C2 to X cmp C1 +- C2 [-Wstrict-overflow]
             while(buf_left>0) {
    [01;32m[K              ^[m[K
      CCLD     test_dset_opt
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    Making all in tools
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    Making all in gif2h5
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
      CCLD     gif2h5
      CCLD     h52gif
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
    libtool: install: warning: remember to run `libtool --finish /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/lib'
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
    libtool: install: warning: remember to run `libtool --finish /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/lib'
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
    libtool: install: (cd /gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/src; /bin/sh /gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libtool  --silent --tag CC --mode=relink gcc -std=c99 -pedantic -Wall -Wextra -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wcast-qual -Wcast-align -Wwrite-strings -Wconversion -Waggregate-return -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wnested-externs -Winline -Wfloat-equal -Wmissing-format-attribute -Wmissing-noreturn -Wpacked -Wdisabled-optimization -Wformat=2 -Wunreachable-code -Wendif-labels -Wdeclaration-after-statement -Wold-style-definition -Winvalid-pch -Wvariadic-macros -Winit-self -Wmissing-include-dirs -Wswitch-default -Wswitch-enum -Wunused-macros -Wunsafe-loop-optimizations -Wc++-compat -Wstrict-overflow -Wlogical-op -Wlarger-than=2048 -Wvla -Wsync-nand -Wframe-larger-than=16384 -Wpacked-bitfield-compat -Wstrict-overflow=5 -Wjump-misses-init -Wdouble-promotion -Wsuggest-attribute=const -Wtrampolines -Wstack-usage=8192 -Wvector-operation-performance -Wsuggest-attribute=pure -Wsuggest-attribute=noreturn -Wsuggest-attribute=format -O3 -version-info 11:0:1 -o libhdf5_hl.la -rpath /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib H5DO.lo H5DS.lo H5IM.lo H5LT.lo H5LTanalyze.lo H5LTparse.lo H5PT.lo H5TB.lo ../../src/libhdf5.la -lz -ldl -lm )
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



```python
os.environ['HDF5_DIR']= hdf5Dir + "/hdf5"
!pip install h5py --user
```

    Collecting h5py
      Using cached h5py-2.6.0.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): numpy>=1.6.1 in /usr/local/src/bluemix_jupyter_bundle.v3/notebook/lib/python2.7/site-packages (from h5py)
    Requirement already satisfied (use --upgrade to upgrade): six in /usr/local/src/bluemix_jupyter_bundle.v3/notebook/lib/python2.7/site-packages (from h5py)
    Installing collected packages: h5py
      Running setup.py install for h5py ... [?25l- \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ done
    [?25hSuccessfully installed h5py-2.6.0


# Let's work through the h5py Quick Start Guide
[Quick Start Guide](http://docs.h5py.org/en/latest/quick.html)


```python
import h5py
import numpy as np
f = h5py.File("mytestfile2.hdf5", "w")
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
