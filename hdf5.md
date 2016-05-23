
# Example Notebook for Setting up and Installing NETCDF4 Data Format, HDF5 File Format and H5PY on Bluemix

<div class="alert alert-info" role="alert">
  <strong>Background</strong> What started out as a curiosity to help answer a Stack Overflow question about installing h5py on Bluemix turned into this huge notebook hack! :-) In the spirit of sharing to minimize pain, I thought I would document the process to have HDF5 File Format, NECDF4 Data Format and H5PY+NETCDF4 python support within a IBM Bluemix Jupyter notebook.  While these steps require compilation, time and in some cases browser reloading ... fortunately this should be a one time step.  After completion, you'll have the python modules in your global user space python package repo alongwith their pre-requesite native libs.
  
<strong>What does all of this mean??</strong> You'll be able to do python module imports such as <strong>import h5py</strong> OR <strong>import NETCDF4</strong> in any other notebook within this spark service instance.  You'll be able to read HDF5 files.  You'll be able to work wtih NETCDF4 file formats.  Cool!
</div>


```python
# On Linux, Python installations default to the unicode representation of the OS environment (ucs2 vs ucs4)
# Let's check the size of the Unicode character a Python interpreter is using by checking the value of sys.maxunicode
import sys
if sys.maxunicode > 65535:
    print 'UCS4 build'
else:
    print 'UCS2 build'
```

    UCS2 build


## Identify your current working directory
### We will need to declare a few paths


```python
# Author:  Sanjay Joshi (@jStartter) ibm.biz/sanjay_joshi
# Courtesy of jStart - IBM Emerging Technology's client engagement team

import os
import subprocess as sub

p = sub.Popen(['pwd'],stdout=sub.PIPE,stderr=sub.PIPE)
prefix, errors = p.communicate()
prefix = os.sep.join(prefix.split(os.sep)[:-2])
shareDir = prefix + "/.local/share"
hdf5Dir = shareDir + "/notebook_hdf5"
netcdfDir = shareDir + "/notebook_netcdf"
print "prefix = " + prefix
print "shareDir = " + shareDir
print "hdf5Dir = " + hdf5Dir
print "netcdfDir = " + netcdfDir
```

    prefix = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5
    shareDir = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share
    hdf5Dir = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5
    netcdfDir = /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf



```python
# Temp Testing cell to help reset setup
# Uncomment and Run if you want to test modifications/tweaks to this notebook and need to reset to an uninstalled state
#!rm -rf $shareDir
#!rm -rf $prefix/.local/lib/python2.7/site-packages/*
#!rm -rf *
```


```python
# Let's test to see if we've already built HDF5
isHDF5Installed = os.path.isfile(hdf5Dir + "/hdf5/bin/h5cc")
if isHDF5Installed:
    print "Congratulations! HDF5 is already installed within your notebook user space"
else:
    print "HDF5 is NOT installed within this notebook's user space"
    
# Let's test to see if we've already built netcdf
isNETCDFInstalled = os.path.isfile(netcdfDir + "/netcdf/bin/nc-config")
if isNETCDFInstalled:
    print "Congratulations! NETCDF is already installed within your notebook user space"
    !$netcdfDir/netcdf/bin/nc-config --version
else:
    print "NETCDF is NOT installed within this notebook's user space"
```

    HDF5 is NOT installed within this notebook's user space
    NETCDF is NOT installed within this notebook's user space



```python
if not isHDF5Installed:
    !mkdir $shareDir
    !mkdir $hdf5Dir
    !mkdir $hdf5Dir/hdf5
    print "HDF5 directories created to facilitate HDF5 build and install"
```

    HDF5 directories created to facilitate HDF5 build and install



```python
if not isNETCDFInstalled:
    !mkdir $netcdfDir
    !mkdir $netcdfDir/netcdf
    print "NETCDF directories created to facilitate netcdf build and install"
```

    NETCDF directories created to facilitate netcdf build and install


## Fetch Snapshot of hdf5 tar gzip file


```python
if not isHDF5Installed:
    !wget https://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz -O $hdf5Dir/hdf5-1.8.17.tar.gz
```

    --2016-05-23 00:31:27--  https://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz
    Resolving www.hdfgroup.org (www.hdfgroup.org)... 50.28.50.143
    Connecting to www.hdfgroup.org (www.hdfgroup.org)|50.28.50.143|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 12304149 (12M) [application/x-gzip]
    Saving to: '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz'
    
    100%[======================================>] 12,304,149  2.23MB/s   in 5.4s   
    
    2016-05-23 00:31:32 (2.19 MB/s) - '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17.tar.gz' saved [12304149/12304149]
    


## Fetch Snapshot of netcdf tar gzip file


```python
if not isNETCDFInstalled:
    !wget https://github.com/Unidata/netcdf-c/archive/v4.4.0.tar.gz -O $netcdfDir/v4.4.0.tar.gz
```

    --2016-05-23 00:31:32--  https://github.com/Unidata/netcdf-c/archive/v4.4.0.tar.gz
    Resolving github.com (github.com)... 192.30.252.129
    Connecting to github.com (github.com)|192.30.252.129|:443... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: https://codeload.github.com/Unidata/netcdf-c/tar.gz/v4.4.0 [following]
    --2016-05-23 00:31:33--  https://codeload.github.com/Unidata/netcdf-c/tar.gz/v4.4.0
    Resolving codeload.github.com (codeload.github.com)... 192.30.252.161
    Connecting to codeload.github.com (codeload.github.com)|192.30.252.161|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: unspecified [application/x-gzip]
    Saving to: '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/v4.4.0.tar.gz'
    
        [              <=>                      ] 17,487,357  4.26MB/s   in 3.9s   
    
    2016-05-23 00:31:37 (4.26 MB/s) - '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/v4.4.0.tar.gz' saved [17487357]
    


## Untar (extract) file


```python
if not isHDF5Installed:
    !tar -zxvf $hdf5Dir/hdf5-1.8.17.tar.gz -C $hdf5Dir >/dev/null
    hdf5SrcDir = hdf5Dir + "/hdf5-1.8.17"
    print hdf5SrcDir
```

    /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17



```python
if not isHDF5Installed:
    !ls -al $hdf5SrcDir
```

    total 2376
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 .
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 ..
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
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 bin
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 c++
    drwxr-xr-x  5 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 config
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users 1034850 May 10 16:24 configure
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  105916 May 10 16:24 configure.ac
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 examples
    drwxr-xr-x  6 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 fortran
    drwxr-xr-x  8 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 hl
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 m4
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 release_docs
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   16384 May 23 00:31 src
    drwxr-xr-x  3 s1a2-472d95bcebf7db-bf066087ecf5 users    8192 May 23 00:31 test
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 testpar
    drwxr-xr-x 14 s1a2-472d95bcebf7db-bf066087ecf5 users    4096 May 23 00:31 tools



```python
if not isNETCDFInstalled:
    !tar -zxvf $netcdfDir/v4.4.0.tar.gz -C $netcdfDir >/dev/null
    netcdfSrcDir = netcdfDir + "/netcdf-c-4.4.0"
    print netcdfSrcDir
```

    /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0



```python
if not isNETCDFInstalled:
    !ls -al $netcdfSrcDir
```

    total 1584
    drwxr-xr-x 22 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 .
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 May 23 00:31 ..
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    292 Jan 13 15:40 .gitignore
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   1572 Jan 13 15:40 .travis.yml
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    944 Jan 13 15:40 .travis.yml.old
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   3427 Jan 13 15:40 CMakeInstallation.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  56801 Jan 13 15:40 CMakeLists.txt
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   3186 Jan 13 15:40 COMPILE.cmake.txt
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  11363 Jan 13 15:40 CONTRIBUTING.html
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   2032 Jan 13 15:40 COPYRIGHT
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    666 Jan 13 15:40 CTestConfig.cmake.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    209 Jan 13 15:40 CTestCustom.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    261 Jan 13 15:40 FixBundle.cmake.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  19394 Jan 13 15:40 INSTALL.md
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    547 Jan 13 15:40 Make0
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   7605 Jan 13 15:40 Makefile.am
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  40657 Jan 13 15:40 Makefile.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users     76 Jan 13 15:40 PostInstall.cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   4752 Jan 13 15:40 README.md
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users  82319 Jan 13 15:40 RELEASE_NOTES.md
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  18621 Jan 13 15:40 acinclude.m4
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  43973 Jan 13 15:40 aclocal.m4
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   4298 Jan 13 15:40 cf
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   1974 Jan 13 15:40 cf.cmake
    drwxr-xr-x  3 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 cmake
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   1042 Jan 13 15:40 cmake_uninstall.cmake.in
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users   7333 Jan 13 15:40 compile
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users  42938 Jan 13 15:40 config.guess
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   8986 Jan 13 15:40 config.h.cmake.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  14330 Jan 13 15:40 config.h.in
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users  35987 Jan 13 15:40 config.sub
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users 641725 Jan 13 15:40 configure
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  51320 Jan 13 15:40 configure.ac
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users  23566 Jan 13 15:40 depcomp
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 docs
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users  19722 Jan 13 15:40 dods.m4
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 examples
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 h5_test
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 include
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users  14675 Jan 13 15:40 install-sh
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    469 Jan 13 15:40 lib_flags.am
    drwxr-xr-x  3 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 libdap2
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 libdispatch
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 liblib
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    835 Jan 13 15:40 libnetcdf.settings.in
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 libsrc
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 libsrc4
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 libsrcp
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users 324089 Jan 13 15:40 ltmain.sh
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 m4
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users    242 Jan 13 15:40 mclean
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users   6872 Jan 13 15:40 missing
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   5620 Jan 13 15:40 nc-config.cmake.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   5072 Jan 13 15:40 nc-config.in
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 nc_test
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   8192 Jan 13 15:40 nc_test4
    drwxr-xr-x 11 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 ncdap_test
    drwxr-xr-x  4 s1a2-472d95bcebf7db-bf066087ecf5 users   8192 Jan 13 15:40 ncdump
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 ncgen
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 ncgen3
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 nctest
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users   1124 Jan 13 15:40 netCDFConfig.cmake.in
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    275 Jan 13 15:40 netcdf.pc.in
    drwxr-xr-x  2 s1a2-472d95bcebf7db-bf066087ecf5 users   4096 Jan 13 15:40 oc2
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users   2719 Jan 13 15:40 postinstall.sh.in
    -rwxr-xr-x  1 s1a2-472d95bcebf7db-bf066087ecf5 users   4640 Jan 13 15:40 test-driver
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    281 Jan 13 15:40 test_prog.F
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    268 Jan 13 15:40 test_prog.c
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    285 Jan 13 15:40 test_prog.f90
    -rw-r--r--  1 s1a2-472d95bcebf7db-bf066087ecf5 users    729 Jan 13 15:40 wjna


# HDF5 support is useful for NETCDF4 and should be built first

## Run Configure for the HDF5 source directory


```python
if not isHDF5Installed:
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
    checking for config /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/config/site-specific/host-yp-spark-dal09-env5-0028... no
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
    		  Configured on: Mon May 23 00:31:58 CDT 2016
    		  Configured by: s1a2-472d95bcebf7db-bf066087ecf5@yp-spark-dal09-env5-0028
    		 Configure mode: production
    		    Host system: x86_64-unknown-linux-gnu
    	      Uname information: Linux yp-spark-dal09-env5-0028 3.10.0-229.14.1.el7.x86_64 #1 SMP Tue Sep 15 15:05:51 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
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
if not isHDF5Installed:
    # Let's make the build and install process as quiet as possible.  Removing all of the H5_CFFLAGS compiler warning settings
    !rm config.status.new 2>/dev/null
    !cat config.status | sed -n '1h;1!H;${;g;s/"\-std.*O3"/"\-std=c99"/g;p;}' | sed 's/&amp;/\&/g' | sed 's/&lt;/\</g' | sed 's/&gt;/\>/g' > config.status.new
    !rm config.status
    !mv config.status.new config.status
```

## Let's build HDF5


```python
# WARNING:  This cell will take a while ... ~10-12 mins with high CPU on the browser.  
# Your browser may even become unresponsive for a period of time.  Just be patient until the cell execution is complete.  Go grab something to drink.
# It is basically configuring and compiling the native hdf5 libs that are required by H5py 
# NOTE:  You may need to refresh your browser after this cell completes.  Carefully monitor the kernel indicator in the upper right as well.
#        The good news is that this is a one time operation. After the native libs are built, they will be available to all existing and new notebooks within this Spark Instance.
if not isHDF5Installed:
    !make -w -j2 && make -w install
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
      CC       H5system.lo
      CC       H5dbg.lo
      CC       H5trace.lo
      CC       H5timer.lo
      CC       H5A.lo
      CC       H5Abtree2.lo
      CC       H5Adeprec.lo
      CC       H5Adense.lo
      CC       H5Aint.lo
      CC       H5Atest.lo
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
      CC       H5Dbtree.lo
      CC       H5D.lo
      CC       H5Dchunk.lo
      CC       H5Dcompact.lo
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
      CC       H5Dio.lo
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
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dio.c:[m[K In function '[01m[KH5D__read[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/src/H5Dio.c:424:5:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[KH5T_patch_vlen_file[m[K' [-Wimplicit-function-declaration]
         H5T_patch_vlen_file(dataset->shared->type, dataset->oloc.file);
    [01;32m[K     ^[m[K
      CC       H5Dmpio.lo
      CC       H5Dlayout.lo
      CC       H5Dscatgath.lo
      CC       H5Doh.lo
      CC       H5Dtest.lo
      CC       H5Dselect.lo
      CC       H5Edeprec.lo
      CC       H5E.lo
      CC       H5Eint.lo
      CC       H5Fint.lo
      CC       H5F.lo
      CC       H5Fcwfs.lo
      CC       H5Faccum.lo
      CC       H5Fefc.lo
      CC       H5Fdbg.lo
      CC       H5Fio.lo
      CC       H5Ffake.lo
      CC       H5Fmount.lo
      CC       H5Fmpi.lo
      CC       H5Fsfile.lo
      CC       H5Fquery.lo
      CC       H5Fsuper.lo
      CC       H5Fsuper_cache.lo
      CC       H5FD.lo
      CC       H5Ftest.lo
      CC       H5FDcore.lo
      CC       H5FDdirect.lo
      CC       H5FDfamily.lo
      CC       H5FDint.lo
      CC       H5FDmpi.lo
      CC       H5FDlog.lo
      CC       H5FDmulti.lo
      CC       H5FDmpio.lo
      CC       H5FDsec2.lo
      CC       H5FDspace.lo
      CC       H5FDstdio.lo
      CC       H5FL.lo
      CC       H5FS.lo
      CC       H5FO.lo
      CC       H5FSdbg.lo
      CC       H5FScache.lo
      CC       H5FSstat.lo
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
      CC       H5FStest.lo
      CC       H5G.lo
      CC       H5Gbtree2.lo
      CC       H5Gcache.lo
      CC       H5Gcompact.lo
      CC       H5Gdense.lo
      CC       H5Gdeprec.lo
      CC       H5Gint.lo
      CC       H5Gent.lo
      CC       H5Glink.lo
      CC       H5Gloc.lo
      CC       H5Gnode.lo
      CC       H5Gname.lo
      CC       H5Goh.lo
      CC       H5Gobj.lo
      CC       H5Groot.lo
      CC       H5Gstab.lo
      CC       H5Gtraverse.lo
      CC       H5Gtest.lo
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
      CC       H5HFsection.lo
      CC       H5HFman.lo
      CC       H5HFspace.lo
      CC       H5HFstat.lo
      CC       H5HFtest.lo
      CC       H5HFtiny.lo
      CC       H5HGcache.lo
      CC       H5HG.lo
      CC       H5HGdbg.lo
      CC       H5HGquery.lo
      CC       H5HL.lo
      CC       H5HLcache.lo
      CC       H5HLint.lo
      CC       H5HLdbg.lo
      CC       H5I.lo
      CC       H5HP.lo
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
      CC       H5MF.lo
      CC       H5make_libsettings.o
      CC       H5MFaggr.lo
      CC       H5MFsection.lo
      CC       H5MFdbg.lo
      CC       H5MM.lo
      CC       H5MP.lo
      CC       H5MPtest.lo
      CC       H5O.lo
      CC       H5Oainfo.lo
      CC       H5Oalloc.lo
      CC       H5Oattr.lo
      CC       H5Obogus.lo
      CC       H5Oattribute.lo
      CC       H5Obtreek.lo
      CC       H5Ocache.lo
      CC       H5Ochunk.lo
      CC       H5Ocont.lo
      CC       H5Odbg.lo
      CC       H5Ocopy.lo
      CC       H5Odtype.lo
      CC       H5Odrvinfo.lo
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
      CC       H5Orefcount.lo
      CC       H5Opline.lo
      CC       H5Oshared.lo
      CC       H5Osdspace.lo
      CC       H5Oshmesg.lo
      CC       H5Ostab.lo
      CC       H5Ounknown.lo
      CC       H5Otest.lo
      CC       H5P.lo
      CC       H5Pacpl.lo
      CC       H5Pdcpl.lo
      CC       H5Pdapl.lo
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
      CC       H5Pgcpl.lo
      CC       H5Pfmpl.lo
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
      CC       H5S.lo
      CC       H5RS.lo
      CC       H5Sall.lo
      CC       H5Sdbg.lo
      CC       H5Smpio.lo
      CC       H5Shyper.lo
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
      CC       H5Tarray.lo
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
      CC       H5Tbit.lo
      CC       H5Tcompound.lo
      CC       H5Tcommit.lo
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
      CC       H5Zdeflate.lo
      CC       H5Z.lo
      CC       H5Zfletcher32.lo
      CC       H5Znbit.lo
      CC       H5Zshuffle.lo
      CC       H5Zszip.lo
      CC       H5Zscaleoffset.lo
      CC       H5Ztrans.lo
      CCLD     H5detect
      CCLD     H5make_libsettings
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH`echo  |                  \
    	sed -e 's/-L/:/g' -e 's/ //g'`"                               \
     ./H5make_libsettings > H5lib_settings.c  ||                               \
        (test $HDF5_Make_Ignore && echo "*** Error ignored") ||          \
        (rm -f H5lib_settings.c ; exit 1)
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH`echo  |                  \
    	sed -e 's/-L/:/g' -e 's/ //g'`"                               \
     ./H5detect > H5Tinit.c  ||                               \
        (test $HDF5_Make_Ignore && echo "*** Error ignored") ||          \
        (rm -f H5Tinit.c ; exit 1)
      CC       H5lib_settings.lo
      CC       H5Tinit.lo
      CCLD     libhdf5.la
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/src'
    Making all in test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
      CC       dynlib1.lo
      CC       dynlib2.lo
      CC       dynlib3.lo
      CC       h5test.lo
      CC       cache_common.lo
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
      CC       trefer.o
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
      CC       trefstr.o
      CC       tskiplist.o
      CC       tselect.o
      CC       tsohm.o
      CC       ttime.o
      CC       ttst.o
      CC       tunicode.o
      CC       tvltypes.o
      CC       tvlstr.o
      CC       lheap.o
      CC       stab.o
      CC       ohdr.o
      CC       gheap.o
      CC       cache.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache.c:[m[K In function '[01m[Kcheck_auto_cache_resize_input_errs[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache.c:25757:9:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[Kresize_configs_are_equal[m[K' [-Wimplicit-function-declaration]
             } else if ( ! resize_configs_are_equal(&test_auto_size_ctl, \
    [01;32m[K         ^[m[K
      CC       cache_api.o
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache_api.c:[m[K In function '[01m[Kcheck_fapl_mdc_api_calls[m[K':
    [01m[K/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5-1.8.17/test/cache_api.c:265:2:[m[K [01;35m[Kwarning: [m[Kimplicit declaration of function '[01m[Kresize_configs_are_equal[m[K' [-Wimplicit-function-declaration]
      if ( ! resize_configs_are_equal(&default_auto_size_ctl, \
    [01;32m[K  ^[m[K
      CC       pool.o
      CC       accum.o
      CC       hyperslab.o
      CC       istore.o
      CC       bittests.o
      CC       dtypes.o
      CC       dt_arith.o
      CC       cmpd_dset.o
      CC       dsets.o
      CC       filter_fail.o
      CC       extend.o
      CC       external.o
      CC       efc.o
      CC       objcopy.o
      CC       links.o
      CC       unlink.o
      CC       big.o
      CC       mtime.o
      CC       mount.o
      CC       fillval.o
      CC       flush1.o
      CC       app_ref.o
      CC       flush2.o
      CC       set_extent.o
      CC       enum.o
      CC       ttsafe.o
      CC       ttsafe_dcreate.o
      CC       ttsafe_error.o
      CC       ttsafe_cancel.o
      CC       getname.o
      CC       ttsafe_acreate.o
      CC       vfd.o
      CC       ntypes.o
      CC       dangle.o
      CC       dtransform.o
      CC       reserved.o
      CC       freespace.o
      CC       cross_read.o
      CC       mf.o
      CC       btree2.o
      CC       fheap.o
      CC       file_image.o
      CC       unregister.o
      CC       error_test.o
      CC       tcheck_version.o
      CC       err_compat.o
      CC       links_env.o
      CC       testmeta.o
      CC       plugin.o
      CCLD     libdynlib1.la
      CCLD     libdynlib2.la
      CCLD     libdynlib3.la
      CCLD     libh5test.la
      CCLD     lheap
      CCLD     testhdf5
      CCLD     ohdr
      CCLD     stab
      CCLD     cache
      CCLD     gheap
      CCLD     cache_api
      CCLD     pool
      CCLD     accum
      CCLD     hyperslab
      CCLD     istore
      CCLD     bittests
      CCLD     dt_arith
      CCLD     dtypes
      CCLD     cmpd_dset
      CCLD     dsets
      CCLD     filter_fail
      CCLD     extend
      CCLD     efc
      CCLD     external
      CCLD     links
      CCLD     objcopy
      CCLD     big
      CCLD     unlink
      CCLD     fillval
      CCLD     mtime
      CCLD     flush1
      CCLD     mount
      CCLD     flush2
      CCLD     app_ref
      CCLD     set_extent
      CCLD     enum
      CCLD     ttsafe
      CCLD     getname
      CCLD     ntypes
      CCLD     vfd
      CCLD     dangle
      CCLD     dtransform
      CCLD     cross_read
      CCLD     reserved
      CCLD     freespace
      CCLD     mf
      CCLD     fheap
      CCLD     btree2
      CCLD     file_image
      CCLD     unregister
      CCLD     err_compat
      CCLD     error_test
      CCLD     tcheck_version
      CCLD     testmeta
      CCLD     plugin
      CCLD     links_env
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/test'
    Making all in tools
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools'
    Making all in lib
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/lib'
      CC       h5tools.lo
      CC       h5tools_dump.lo
      CC       h5tools_str.lo
      CC       h5tools_utils.lo
      CC       h5diff_array.lo
      CC       h5diff.lo
      CC       h5diff_attr.lo
      CC       h5diff_util.lo
      CC       h5diff_dset.lo
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
      CC       h5diffgentest.o
      CCLD     h5diff
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
      CC       h5dumpgentest.o
      CC       h5dump_xml.o
      CC       binread.o
      CCLD     binread
      CCLD     h5dump
      CCLD     h5dumpgentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5dump'
    Making all in misc
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
      CC       h5repart.o
      CC       h5debug.o
      CC       h5mkgrp.o
      CC       h5repart_gentest.o
      CC       repart_test.o
      CC       talign.o
      CCLD     h5repart
      CCLD     h5debug
      CCLD     h5repart_gentest
      CCLD     h5mkgrp
      CCLD     repart_test
      CCLD     talign
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/misc'
    Making all in h5import
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
      CC       h5import.o
      CC       h5importtest.o
      CCLD     h5importtest
      CCLD     h5import
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5import'
    Making all in h5repack
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
      CC       h5repack.o
      CC       dynlib_rpk.lo
      CC       h5repack_copy.o
      CC       h5repack_opttable.o
      CC       h5repack_filters.o
      CC       h5repack_parse.o
      CC       h5repack_refs.o
      CC       h5repack_main.o
      CC       h5repack_verify.o
      CC       testh5repack_detect_szip.o
      CC       h5repacktst.o
      CCLD     libdynlibadd.la
      CCLD     h5repack
      CCLD     testh5repack_detect_szip
      CCLD     h5repacktst
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5repack'
    Making all in h5jam
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
      CC       h5unjam.o
      CC       h5jam.o
      CC       h5jamgentest.o
      CC       tellub.o
      CCLD     h5jam
      CC       getub.o
      CCLD     h5unjam
      CCLD     tellub
      CCLD     h5jamgentest
      CCLD     getub
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5jam'
    Making all in h5copy
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
      CC       h5copygentest.o
      CC       h5copy.o
      CCLD     h5copygentest
      CCLD     h5copy
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5copy'
    Making all in h5stat
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
      CC       h5stat.o
      CC       h5stat_gentest.o
      CCLD     h5stat
      CCLD     h5stat_gentest
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/h5stat'
    Making all in perform
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/tools/perform'
      CC       sio_perf.o
      CC       sio_engine.o
      CC       iopipe.o
      CC       chunk.o
      CC       zip_perf.o
      CC       overhead.o
      CC       perf_meta.o
      CC       perf.o
      CCLD     iopipe
      CCLD     h5perf_serial
      CCLD     chunk
      CCLD     overhead
      CCLD     perf_meta
      CCLD     zip_perf
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
      CC       test_image.o
      CC       test_table.o
      CC       test_file_image.o
      CC       test_ds.o
      CC       test_packet.o
      CC       test_dset_opt.o
      CC       test_packet_vlen.o
      CCLD     test_lite
      CCLD     test_image
      CCLD     test_file_image
      CCLD     test_table
      CCLD     test_ds
      CCLD     test_packet
      CCLD     test_dset_opt
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/test'
    Making all in tools
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools'
    Making all in gif2h5
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/hl/tools/gif2h5'
      CC       gif2mem.o
      CC       gif2hdf.o
      CC       decompress.o
      CC       gifread.o
      CC       hdf2gif.o
      CC       writehdf.o
      CC       hdfgifwr.o
      CC       h52gifgentst.o
      CCLD     gif2h5
      CCLD     h52gifgentst
      CCLD     h52gif
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
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
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
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
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
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
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
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
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


<div class="alert alert-danger" role="alert">
  <strong>WARNING</strong> You may have to reload your browser to continue.  The Make and Make Install process can sometimes cause the notebook to lose it's kernel context.  Don't worry, all of the compiled files will persist across the browser reload.
</div>

## Housekeeping


```python
# Re-evaluate installation status
isHDF5Installed = os.path.isfile(hdf5Dir + "/hdf5/bin/h5cc")

if isHDF5Installed:
    # Remove all of configure generated by-products
    !rm -rf * 2>/dev/null
    # Remove the extracted source folder
    !rm -rf $hdf5Dir/hdf5-1.8.17 2>/dev/null
    # Remove the tar gzip file
    !rm $hdf5Dir/hdf5-1.8.17.tar.gz 2>/dev/null
    print "HDF5 directories for facilitating HDF5 build and install have been removed"
else:
    print "HDF5 build and install seems to have failed.  Cannot local <HDF5_Install_Point>/bin/h5cc"
```

    HDF5 directories for facilitating HDF5 build and install have been removed


# Now Let's take a look @ netCDF4 and build it with HDF5 support ;-)
[Sample Notebooks Exploring netcdf data](http://nbviewer.jupyter.org/url/www.hydro.washington.edu/~jhamman/hydro-logic/downloads/notebooks/2013-10-12-plot-netcdf-data.ipynb)
[Quickstart](http://unidata.github.io/netcdf4-python/#section1)

## Run Configure for the netCDF4 source directory


```python
# Setup Configuration Info
# http://www.unidata.ucar.edu/mailing_lists/archives/netcdfgroup/2011/msg00340.html
# http://www.unidata.ucar.edu/software/netcdf/netcdf-4/newdocs/netcdf-install/Configure.html
os.environ['HDF5_DIR']= hdf5Dir + "/hdf5"
os.environ['CPPFLAGS']= "-I" + hdf5Dir + "/hdf5/include"
os.environ['LDFLAGS']= "-L" + hdf5Dir + "/hdf5/lib"
!$netcdfSrcDir/configure --prefix=$netcdfDir/netcdf --enable-netcdf-4
```

    configure: netCDF 4.4.0
    checking build system type... x86_64-unknown-linux-gnu
    checking host system type... x86_64-unknown-linux-gnu
    checking target system type... x86_64-unknown-linux-gnu
    checking for a BSD-compatible install... /bin/install -c
    checking whether build environment is sane... yes
    checking for a thread-safe mkdir -p... /bin/mkdir -p
    checking for gawk... gawk
    checking whether make sets $(MAKE)... yes
    checking whether make supports nested variables... yes
    checking whether to enable maintainer-specific portions of Makefiles... no
    configure: checking user options
    checking whether a win32 DLL is desired... no
    checking whether a NCIO_MINBLOCKSIZE was specified... 256
    checking if fsync support is enabled... no
    checking if jna bug workaround is enabledd... no
    checking whether extra valgrind tests should be run... no
    checking whether we should build netCDF-4... yes
    checking do we require hdf5 dynamic-loading support... yes
    checking whether reading of HDF4 SD files is to be enabled... no
    checking whether to fetch some sample HDF4 files from Unidata ftp site to test HDF4 reading (requires wget)... no
    checking whether we should attempt to install netcdf-fortran (EXPERIMENTAL)... no
    checking whether extra example tests should be run... no
    checking whether parallel IO tests should be run... no
    checking whether a default chunk size in bytes was specified... 4194304
    checking whether a maximum per-variable cache size for HDF5 was specified... 67108864
    checking whether a number of chunks for the default per-variable cache was specified... 10
    checking whether a default file cache size for HDF5 was specified... 4194304
    checking whether a default file cache maximum number of elements for HDF5 was specified... 1009
    checking whether a default cache preemption for HDF5 was specified... 0.75
    checking whether netCDF-4 logging is enabled... no
    checking whether DAP client is to be built... yes
    checking for style of include used by make... GNU
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
    checking dependency style of gcc... gcc3
    checking for curl_easy_setopt in -lcurl... no
    configure: libcurl not found; disabling remote protocol(s) support
    checking whether dap remote testing should be enabled (default on)... no
    checking whether dap remote authorization testing should be enabled (default off)... no
    checking whether netcdf4 group names should be enabled (default on)... configure: dap groups disabled because dap disabled or netcdf-4 disabled
    no
    checking whether the time-consuming dap tests should be enabled (default off)... no
    checking whether netCDF extra tests should be run (developers only)... yes
    checking whether FFIO will be used... no
    checking whether STDIO will be used... no
    checking whether examples should be built... yes
    checking whether v2 netCDF API should be built... yes
    checking whether the ncgen/ncdump/nccopy should be built... yes
    checking whether test should be built and run... yes
    checking whether large file (> 2GB) tests should be run... no
    checking whether benchmarks should be run (experimental)... no
    checking whether extreme numbers should be used in tests... yes
    checking where to put large temp files if large file tests are run... .
    configure: finding C compiler
    checking for gcc... (cached) gcc
    checking whether we are using the GNU C compiler... (cached) yes
    checking whether gcc accepts -g... (cached) yes
    checking for gcc option to accept ISO C89... (cached) none needed
    checking whether gcc understands -c and -o together... (cached) yes
    checking dependency style of gcc... (cached) gcc3
    checking for an ANSI C-conforming const... yes
    checking whether CURLOPT_USERNAME is defined... no
    checking whether CURLOPT_PASSWORD is defined... no
    checking whether CURLOPT_KEYPASSWD is defined... no
    checking whether CURLINFO_RESPONSE_CODE is defined... no
    checking whether CURLOPT_CHUNK_BGN_FUNCTION is defined... no
    configure: setting up libtool
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
    checking how to convert x86_64-unknown-linux-gnu file names to x86_64-unknown-linux-gnu format... func_convert_file_noop
    checking how to convert x86_64-unknown-linux-gnu file names to toolchain format... func_convert_file_noop
    checking for /bin/ld option to reload object files... -r
    checking for objdump... objdump
    checking how to recognize dependent libraries... pass_all
    checking for dlltool... no
    checking how to associate runtime and link libraries... printf %s\n
    checking for ar... ar
    checking for archiver @FILE support... @
    checking for strip... strip
    checking for ranlib... ranlib
    checking command to parse /bin/nm -B output from gcc object... ok
    checking for sysroot... no
    checking for a working dd... /bin/dd
    checking how to truncate binary pipes... /bin/dd bs=4096 count=1
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
    checking whether stripping libraries is possible... yes
    checking if libtool supports shared libraries... yes
    checking whether to build shared libraries... yes
    checking whether to build static libraries... yes
    configure: finding other utilities
    checking for m4... m4
    checking for doxygen... doxygen
    checking for dot... no
    configure: WARNING: dot not found - will use simple charts in documentation
    checking for special C compiler options needed for large files... no
    checking for _FILE_OFFSET_BITS value needed for large files... no
    configure: displaying some results
    checking CPPFLAGS... -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include
    checking CC CFLAGS... gcc -g -O2
    checking type gcc... gcc is /bin/gcc
    checking CXX... unset
    checking FC... unset
    checking F90... unset
    checking AR... ar
    checking AR_FLAGS... cru
    checking type ar... ar is /bin/ar
    checking NM... /bin/nm -B
    checking NMFLAGS... 
    configure: checking types, headers, and functions
    checking for stdlib.h... (cached) yes
    checking for sys/types.h... (cached) yes
    checking sys/param.h usability... yes
    checking sys/param.h presence... yes
    checking for sys/param.h... yes
    checking for dirent.h that defines DIR... yes
    checking for library containing opendir... none required
    checking for ANSI C header files... (cached) yes
    checking for sys/wait.h that is POSIX.1 compatible... yes
    checking whether time.h and sys/time.h may both be included... yes
    checking locale.h usability... yes
    checking locale.h presence... yes
    checking for locale.h... yes
    checking stdio.h usability... yes
    checking stdio.h presence... yes
    checking for stdio.h... yes
    checking stdarg.h usability... yes
    checking stdarg.h presence... yes
    checking for stdarg.h... yes
    checking errno.h usability... yes
    checking errno.h presence... yes
    checking for errno.h... yes
    checking ctype.h usability... yes
    checking ctype.h presence... yes
    checking for ctype.h... yes
    checking fcntl.h usability... yes
    checking fcntl.h presence... yes
    checking for fcntl.h... yes
    checking malloc.h usability... yes
    checking malloc.h presence... yes
    checking for malloc.h... yes
    checking for stdlib.h... (cached) yes
    checking for string.h... (cached) yes
    checking for strings.h... (cached) yes
    checking for unistd.h... (cached) yes
    checking for sys/stat.h... (cached) yes
    checking getopt.h usability... yes
    checking getopt.h presence... yes
    checking for getopt.h... yes
    checking sys/time.h usability... yes
    checking sys/time.h presence... yes
    checking for sys/time.h... yes
    checking for vprintf... yes
    checking for _doprnt... no
    checking sys/resource.h usability... yes
    checking sys/resource.h presence... yes
    checking for sys/resource.h... yes
    checking for stdbool.h that conforms to C99... yes
    checking for _Bool... yes
    checking for strlcat... no
    checking for strerror... yes
    checking for snprintf... yes
    checking for strchr... yes
    checking for strrchr... yes
    checking for strcat... yes
    checking for strcpy... yes
    checking for strdup... yes
    checking for strcasecmp... yes
    checking for strtod... yes
    checking for strtoll... yes
    checking for strtoull... yes
    checking for strstr... yes
    checking for mkstemp... yes
    checking for rand... yes
    checking for memcmp... yes
    checking for getrlimit... yes
    checking for gettimeofday... yes
    checking for fsync... yes
    checking for MPI_Comm_f2c... no
    checking whether in-memory files are enabled... yes
    checking for memmove... yes
    checking for getpagesize... yes
    checking for sysconf... yes
    checking whether mmap is enabled for in-memory files... no
    checking for stdlib.h... (cached) yes
    checking for unistd.h... (cached) yes
    checking for sys/param.h... (cached) yes
    checking for getpagesize... (cached) yes
    checking for working mmap... yes
    checking for mremap... yes
    checking for size_t... yes
    checking for working alloca.h... yes
    checking for alloca... yes
    checking whether isnan is declared... yes
    checking whether isinf is declared... yes
    checking whether isfinite is declared... yes
    checking whether signbit is declared... yes
    checking for struct stat.st_blksize... yes
    checking for IEEE floating point format... yes
    checking for size_t... (cached) yes
    checking for off_t... yes
    checking for size_t... (cached) yes
    checking for ssize_t... yes
    checking for ptrdiff_t... yes
    checking for uchar... no
    checking for longlong... no
    checking for ushort... yes
    checking for uint... yes
    checking for int64... no
    checking for uint64... no
    checking whether char is unsigned... no
    checking whether byte ordering is bigendian... no
    checking size of short... 2
    checking size of int... 4
    checking size of long... 8
    checking size of long long... 8
    checking size of float... 4
    checking size of double... 8
    checking size of off_t... 8
    checking size of size_t... 8
    checking size of unsigned long long... 8
    checking size of ushort... 2
    checking size of uint... 4
    checking size of unsigned long long... (cached) 8
    checking size of ushort... (cached) 2
    checking size of uint... (cached) 4
    checking size of ptrdiff_t... 8
    checking for library containing deflate... -lz
    checking for floor in -lm... yes
    checking for dlfcn.h... (cached) yes
    checking for dlopen in -ldl... yes
    checking for library containing H5Fflush... -lhdf5
    checking for library containing H5DSis_scale... -lhdf5_hl
    checking hdf5.h usability... yes
    checking hdf5.h presence... yes
    checking for hdf5.h... yes
    checking for H5Pget_fapl_mpiposix... no
    checking for H5Pget_fapl_mpio... no
    checking for H5Pset_deflate... yes
    checking for H5Z_SZIP... no
    checking for H5free_memory... yes
    checking whether parallel io is enabled in hdf5... no
    checking whether parallel I/O is enabled for netcdf-4... yes
    configure: WARNING: Parallel io disabled for netcdf-4 because hdf5 does not support
    checking whether parallel I/O for classic files is to be enabled... no
    checking for unsigned long long int... yes
    checking for long long int... no
    checking for unsigned long long int... (cached) yes
    checking what to call the output of the ftpbin target... binary-netcdf-4.4.0.tar  
    checking if libtool needs -no-undefined flag to build shared libraries... no
    checking value of LIBS... -lhdf5_hl -lhdf5 -ldl -lm -lz 
    checking If compilation is for use with JNA... no
    configure: generating header files and makefiles
    checking that generated files are newer than configure... done
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating nc-config
    config.status: creating netcdf.pc
    config.status: creating libnetcdf.settings
    config.status: creating postinstall.sh
    config.status: creating include/netcdf_meta.h
    config.status: creating include/Makefile
    config.status: creating h5_test/Makefile
    config.status: creating docs/Makefile
    config.status: creating docs/images/Makefile
    config.status: creating libsrc/Makefile
    config.status: creating libsrc4/Makefile
    config.status: creating libsrcp/Makefile
    config.status: creating nctest/Makefile
    config.status: creating nc_test4/Makefile
    config.status: creating nc_test/Makefile
    config.status: creating ncdump/Makefile
    config.status: creating ncgen3/Makefile
    config.status: creating ncgen/Makefile
    config.status: creating examples/Makefile
    config.status: creating examples/C/Makefile
    config.status: creating examples/CDL/Makefile
    config.status: creating oc2/Makefile
    config.status: creating libdap2/Makefile
    config.status: creating libdispatch/Makefile
    config.status: creating liblib/Makefile
    config.status: creating ncdump/cdl/Makefile
    config.status: creating ncdump/expected/Makefile
    config.status: creating ncdap_test/Makefile
    config.status: creating ncdap_test/testdata3/Makefile
    config.status: creating ncdap_test/expected3/Makefile
    config.status: creating ncdap_test/expected4/Makefile
    config.status: creating ncdap_test/expectremote3/Makefile
    config.status: creating ncdap_test/expectremote4/Makefile
    config.status: creating config.h
    config.status: executing depfiles commands
    config.status: executing libtool commands
    # NetCDF C Configuration Summary
    ==============================
    
    # General
    -------
    NetCDF Version:		4.4.0
    Configured On:		Mon May 23 00:42:26 CDT 2016
    Host System:		x86_64-unknown-linux-gnu
    Build Directory: 	/gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work
    Install Prefix:         /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf
    
    # Compiling Options
    -----------------
    C Compiler:		/bin/gcc
    CFLAGS:			-g -O2
    CPPFLAGS:		-I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include
    LDFLAGS:		-L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    AM_CFLAGS:		
    AM_CPPFLAGS:		
    AM_LDFLAGS:		
    Shared Library:		yes
    Static Library:		yes
    Extra libraries:	-lhdf5_hl -lhdf5 -ldl -lm -lz 
    
    # Features
    --------
    NetCDF-2 API:		yes
    HDF4 Support:		no
    NetCDF-4 API:		yes
    NC-4 Parallel Support:	no
    PNetCDF Support:	no
    DAP Support:		no
    Diskless Support:	yes
    MMap Support:		no
    JNA Support:		no


## Let's build netCDF4


```python
!make -w -j2 && make -w install
```

    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make  all-recursive
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making all in include
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    Making all in h5_test
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    Making all in libdispatch
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dparallel.lo -MD -MP -MF .deps/libdispatch_la-dparallel.Tpo -c -o libdispatch_la-dparallel.lo `test -f 'dparallel.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dparallel.c
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dcopy.lo -MD -MP -MF .deps/libdispatch_la-dcopy.Tpo -c -o libdispatch_la-dcopy.lo `test -f 'dcopy.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dcopy.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dcopy.lo -MD -MP -MF .deps/libdispatch_la-dcopy.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dcopy.c  -fPIC -DPIC -o .libs/libdispatch_la-dcopy.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dparallel.lo -MD -MP -MF .deps/libdispatch_la-dparallel.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dparallel.c  -fPIC -DPIC -o .libs/libdispatch_la-dparallel.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dparallel.lo -MD -MP -MF .deps/libdispatch_la-dparallel.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dparallel.c -o libdispatch_la-dparallel.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dparallel.Tpo .deps/libdispatch_la-dparallel.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dfile.lo -MD -MP -MF .deps/libdispatch_la-dfile.Tpo -c -o libdispatch_la-dfile.lo `test -f 'dfile.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dfile.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dcopy.lo -MD -MP -MF .deps/libdispatch_la-dcopy.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dcopy.c -o libdispatch_la-dcopy.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dcopy.Tpo .deps/libdispatch_la-dcopy.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-ddim.lo -MD -MP -MF .deps/libdispatch_la-ddim.Tpo -c -o libdispatch_la-ddim.lo `test -f 'ddim.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`ddim.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dfile.lo -MD -MP -MF .deps/libdispatch_la-dfile.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dfile.c  -fPIC -DPIC -o .libs/libdispatch_la-dfile.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ddim.lo -MD -MP -MF .deps/libdispatch_la-ddim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ddim.c  -fPIC -DPIC -o .libs/libdispatch_la-ddim.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dfile.lo -MD -MP -MF .deps/libdispatch_la-dfile.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dfile.c -o libdispatch_la-dfile.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ddim.lo -MD -MP -MF .deps/libdispatch_la-ddim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ddim.c -o libdispatch_la-ddim.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-ddim.Tpo .deps/libdispatch_la-ddim.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-datt.lo -MD -MP -MF .deps/libdispatch_la-datt.Tpo -c -o libdispatch_la-datt.lo `test -f 'datt.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`datt.c
    mv -f .deps/libdispatch_la-dfile.Tpo .deps/libdispatch_la-dfile.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dattinq.lo -MD -MP -MF .deps/libdispatch_la-dattinq.Tpo -c -o libdispatch_la-dattinq.lo `test -f 'dattinq.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dattinq.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-datt.lo -MD -MP -MF .deps/libdispatch_la-datt.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/datt.c  -fPIC -DPIC -o .libs/libdispatch_la-datt.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattinq.lo -MD -MP -MF .deps/libdispatch_la-dattinq.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattinq.c  -fPIC -DPIC -o .libs/libdispatch_la-dattinq.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-datt.lo -MD -MP -MF .deps/libdispatch_la-datt.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/datt.c -o libdispatch_la-datt.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattinq.lo -MD -MP -MF .deps/libdispatch_la-dattinq.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattinq.c -o libdispatch_la-dattinq.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-datt.Tpo .deps/libdispatch_la-datt.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dattput.lo -MD -MP -MF .deps/libdispatch_la-dattput.Tpo -c -o libdispatch_la-dattput.lo `test -f 'dattput.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dattput.c
    mv -f .deps/libdispatch_la-dattinq.Tpo .deps/libdispatch_la-dattinq.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dattget.lo -MD -MP -MF .deps/libdispatch_la-dattget.Tpo -c -o libdispatch_la-dattget.lo `test -f 'dattget.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dattget.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattget.lo -MD -MP -MF .deps/libdispatch_la-dattget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattget.c  -fPIC -DPIC -o .libs/libdispatch_la-dattget.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattput.lo -MD -MP -MF .deps/libdispatch_la-dattput.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattput.c  -fPIC -DPIC -o .libs/libdispatch_la-dattput.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattget.lo -MD -MP -MF .deps/libdispatch_la-dattget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattget.c -o libdispatch_la-dattget.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dattput.lo -MD -MP -MF .deps/libdispatch_la-dattput.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dattput.c -o libdispatch_la-dattput.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dattget.Tpo .deps/libdispatch_la-dattget.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-derror.lo -MD -MP -MF .deps/libdispatch_la-derror.Tpo -c -o libdispatch_la-derror.lo `test -f 'derror.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`derror.c
    mv -f .deps/libdispatch_la-dattput.Tpo .deps/libdispatch_la-dattput.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dvar.lo -MD -MP -MF .deps/libdispatch_la-dvar.Tpo -c -o libdispatch_la-dvar.lo `test -f 'dvar.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dvar.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-derror.lo -MD -MP -MF .deps/libdispatch_la-derror.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/derror.c  -fPIC -DPIC -o .libs/libdispatch_la-derror.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvar.lo -MD -MP -MF .deps/libdispatch_la-dvar.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvar.c  -fPIC -DPIC -o .libs/libdispatch_la-dvar.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-derror.lo -MD -MP -MF .deps/libdispatch_la-derror.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/derror.c -o libdispatch_la-derror.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvar.lo -MD -MP -MF .deps/libdispatch_la-dvar.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvar.c -o libdispatch_la-dvar.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-derror.Tpo .deps/libdispatch_la-derror.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dvarget.lo -MD -MP -MF .deps/libdispatch_la-dvarget.Tpo -c -o libdispatch_la-dvarget.lo `test -f 'dvarget.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dvarget.c
    mv -f .deps/libdispatch_la-dvar.Tpo .deps/libdispatch_la-dvar.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dvarput.lo -MD -MP -MF .deps/libdispatch_la-dvarput.Tpo -c -o libdispatch_la-dvarput.lo `test -f 'dvarput.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dvarput.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarput.lo -MD -MP -MF .deps/libdispatch_la-dvarput.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarput.c  -fPIC -DPIC -o .libs/libdispatch_la-dvarput.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarget.lo -MD -MP -MF .deps/libdispatch_la-dvarget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarget.c  -fPIC -DPIC -o .libs/libdispatch_la-dvarget.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarput.lo -MD -MP -MF .deps/libdispatch_la-dvarput.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarput.c -o libdispatch_la-dvarput.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarget.lo -MD -MP -MF .deps/libdispatch_la-dvarget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarget.c -o libdispatch_la-dvarget.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dvarput.Tpo .deps/libdispatch_la-dvarput.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dvarinq.lo -MD -MP -MF .deps/libdispatch_la-dvarinq.Tpo -c -o libdispatch_la-dvarinq.lo `test -f 'dvarinq.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dvarinq.c
    mv -f .deps/libdispatch_la-dvarget.Tpo .deps/libdispatch_la-dvarget.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-ddispatch.lo -MD -MP -MF .deps/libdispatch_la-ddispatch.Tpo -c -o libdispatch_la-ddispatch.lo `test -f 'ddispatch.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`ddispatch.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ddispatch.lo -MD -MP -MF .deps/libdispatch_la-ddispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ddispatch.c  -fPIC -DPIC -o .libs/libdispatch_la-ddispatch.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarinq.lo -MD -MP -MF .deps/libdispatch_la-dvarinq.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarinq.c  -fPIC -DPIC -o .libs/libdispatch_la-dvarinq.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ddispatch.lo -MD -MP -MF .deps/libdispatch_la-ddispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ddispatch.c -o libdispatch_la-ddispatch.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvarinq.lo -MD -MP -MF .deps/libdispatch_la-dvarinq.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvarinq.c -o libdispatch_la-dvarinq.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-ddispatch.Tpo .deps/libdispatch_la-ddispatch.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nclog.lo -MD -MP -MF .deps/libdispatch_la-nclog.Tpo -c -o libdispatch_la-nclog.lo `test -f 'nclog.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nclog.c
    mv -f .deps/libdispatch_la-dvarinq.Tpo .deps/libdispatch_la-dvarinq.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dstring.lo -MD -MP -MF .deps/libdispatch_la-dstring.Tpo -c -o libdispatch_la-dstring.lo `test -f 'dstring.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dstring.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclog.lo -MD -MP -MF .deps/libdispatch_la-nclog.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclog.c  -fPIC -DPIC -o .libs/libdispatch_la-nclog.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dstring.lo -MD -MP -MF .deps/libdispatch_la-dstring.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dstring.c  -fPIC -DPIC -o .libs/libdispatch_la-dstring.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclog.lo -MD -MP -MF .deps/libdispatch_la-nclog.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclog.c -o libdispatch_la-nclog.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dstring.lo -MD -MP -MF .deps/libdispatch_la-dstring.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dstring.c -o libdispatch_la-dstring.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-nclog.Tpo .deps/libdispatch_la-nclog.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dutf8proc.lo -MD -MP -MF .deps/libdispatch_la-dutf8proc.Tpo -c -o libdispatch_la-dutf8proc.lo `test -f 'dutf8proc.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dutf8proc.c
    mv -f .deps/libdispatch_la-dstring.Tpo .deps/libdispatch_la-dstring.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-ncuri.lo -MD -MP -MF .deps/libdispatch_la-ncuri.Tpo -c -o libdispatch_la-ncuri.lo `test -f 'ncuri.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`ncuri.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncuri.lo -MD -MP -MF .deps/libdispatch_la-ncuri.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncuri.c  -fPIC -DPIC -o .libs/libdispatch_la-ncuri.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dutf8proc.lo -MD -MP -MF .deps/libdispatch_la-dutf8proc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dutf8proc.c  -fPIC -DPIC -o .libs/libdispatch_la-dutf8proc.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncuri.lo -MD -MP -MF .deps/libdispatch_la-ncuri.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncuri.c -o libdispatch_la-ncuri.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dutf8proc.lo -MD -MP -MF .deps/libdispatch_la-dutf8proc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dutf8proc.c -o libdispatch_la-dutf8proc.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-ncuri.Tpo .deps/libdispatch_la-ncuri.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nclist.lo -MD -MP -MF .deps/libdispatch_la-nclist.Tpo -c -o libdispatch_la-nclist.lo `test -f 'nclist.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nclist.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclist.lo -MD -MP -MF .deps/libdispatch_la-nclist.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclist.c  -fPIC -DPIC -o .libs/libdispatch_la-nclist.o
    mv -f .deps/libdispatch_la-dutf8proc.Tpo .deps/libdispatch_la-dutf8proc.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-ncbytes.lo -MD -MP -MF .deps/libdispatch_la-ncbytes.Tpo -c -o libdispatch_la-ncbytes.lo `test -f 'ncbytes.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`ncbytes.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclist.lo -MD -MP -MF .deps/libdispatch_la-nclist.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclist.c -o libdispatch_la-nclist.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-nclist.Tpo .deps/libdispatch_la-nclist.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nchashmap.lo -MD -MP -MF .deps/libdispatch_la-nchashmap.Tpo -c -o libdispatch_la-nchashmap.lo `test -f 'nchashmap.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nchashmap.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nchashmap.lo -MD -MP -MF .deps/libdispatch_la-nchashmap.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nchashmap.c  -fPIC -DPIC -o .libs/libdispatch_la-nchashmap.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncbytes.lo -MD -MP -MF .deps/libdispatch_la-ncbytes.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncbytes.c  -fPIC -DPIC -o .libs/libdispatch_la-ncbytes.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncbytes.lo -MD -MP -MF .deps/libdispatch_la-ncbytes.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncbytes.c -o libdispatch_la-ncbytes.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nchashmap.lo -MD -MP -MF .deps/libdispatch_la-nchashmap.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nchashmap.c -o libdispatch_la-nchashmap.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-ncbytes.Tpo .deps/libdispatch_la-ncbytes.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nctime.lo -MD -MP -MF .deps/libdispatch_la-nctime.Tpo -c -o libdispatch_la-nctime.lo `test -f 'nctime.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nctime.c
    mv -f .deps/libdispatch_la-nchashmap.Tpo .deps/libdispatch_la-nchashmap.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dsubstrate.lo -MD -MP -MF .deps/libdispatch_la-dsubstrate.Tpo -c -o libdispatch_la-dsubstrate.lo `test -f 'dsubstrate.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dsubstrate.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nctime.lo -MD -MP -MF .deps/libdispatch_la-nctime.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nctime.c  -fPIC -DPIC -o .libs/libdispatch_la-nctime.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dsubstrate.lo -MD -MP -MF .deps/libdispatch_la-dsubstrate.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dsubstrate.c  -fPIC -DPIC -o .libs/libdispatch_la-dsubstrate.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nctime.lo -MD -MP -MF .deps/libdispatch_la-nctime.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nctime.c -o libdispatch_la-nctime.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dsubstrate.lo -MD -MP -MF .deps/libdispatch_la-dsubstrate.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dsubstrate.c -o libdispatch_la-dsubstrate.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-nctime.Tpo .deps/libdispatch_la-nctime.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nc.lo -MD -MP -MF .deps/libdispatch_la-nc.Tpo -c -o libdispatch_la-nc.lo `test -f 'nc.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nc.c
    mv -f .deps/libdispatch_la-dsubstrate.Tpo .deps/libdispatch_la-dsubstrate.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-nclistmgr.lo -MD -MP -MF .deps/libdispatch_la-nclistmgr.Tpo -c -o libdispatch_la-nclistmgr.lo `test -f 'nclistmgr.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`nclistmgr.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclistmgr.lo -MD -MP -MF .deps/libdispatch_la-nclistmgr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclistmgr.c  -fPIC -DPIC -o .libs/libdispatch_la-nclistmgr.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nc.lo -MD -MP -MF .deps/libdispatch_la-nc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nc.c  -fPIC -DPIC -o .libs/libdispatch_la-nc.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nclistmgr.lo -MD -MP -MF .deps/libdispatch_la-nclistmgr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nclistmgr.c -o libdispatch_la-nclistmgr.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-nc.lo -MD -MP -MF .deps/libdispatch_la-nc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/nc.c -o libdispatch_la-nc.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-nclistmgr.Tpo .deps/libdispatch_la-nclistmgr.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dgroup.lo -MD -MP -MF .deps/libdispatch_la-dgroup.Tpo -c -o libdispatch_la-dgroup.lo `test -f 'dgroup.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dgroup.c
    mv -f .deps/libdispatch_la-nc.Tpo .deps/libdispatch_la-nc.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dvlen.lo -MD -MP -MF .deps/libdispatch_la-dvlen.Tpo -c -o libdispatch_la-dvlen.lo `test -f 'dvlen.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dvlen.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvlen.lo -MD -MP -MF .deps/libdispatch_la-dvlen.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvlen.c  -fPIC -DPIC -o .libs/libdispatch_la-dvlen.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dgroup.lo -MD -MP -MF .deps/libdispatch_la-dgroup.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dgroup.c  -fPIC -DPIC -o .libs/libdispatch_la-dgroup.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dvlen.lo -MD -MP -MF .deps/libdispatch_la-dvlen.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dvlen.c -o libdispatch_la-dvlen.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dgroup.lo -MD -MP -MF .deps/libdispatch_la-dgroup.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dgroup.c -o libdispatch_la-dgroup.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dvlen.Tpo .deps/libdispatch_la-dvlen.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dcompound.lo -MD -MP -MF .deps/libdispatch_la-dcompound.Tpo -c -o libdispatch_la-dcompound.lo `test -f 'dcompound.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dcompound.c
    mv -f .deps/libdispatch_la-dgroup.Tpo .deps/libdispatch_la-dgroup.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dtype.lo -MD -MP -MF .deps/libdispatch_la-dtype.Tpo -c -o libdispatch_la-dtype.lo `test -f 'dtype.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dtype.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dtype.lo -MD -MP -MF .deps/libdispatch_la-dtype.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dtype.c  -fPIC -DPIC -o .libs/libdispatch_la-dtype.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dcompound.lo -MD -MP -MF .deps/libdispatch_la-dcompound.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dcompound.c  -fPIC -DPIC -o .libs/libdispatch_la-dcompound.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dtype.lo -MD -MP -MF .deps/libdispatch_la-dtype.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dtype.c -o libdispatch_la-dtype.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dcompound.lo -MD -MP -MF .deps/libdispatch_la-dcompound.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dcompound.c -o libdispatch_la-dcompound.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dtype.Tpo .deps/libdispatch_la-dtype.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-denum.lo -MD -MP -MF .deps/libdispatch_la-denum.Tpo -c -o libdispatch_la-denum.lo `test -f 'denum.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`denum.c
    mv -f .deps/libdispatch_la-dcompound.Tpo .deps/libdispatch_la-dcompound.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-dopaque.lo -MD -MP -MF .deps/libdispatch_la-dopaque.Tpo -c -o libdispatch_la-dopaque.lo `test -f 'dopaque.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dopaque.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dopaque.lo -MD -MP -MF .deps/libdispatch_la-dopaque.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dopaque.c  -fPIC -DPIC -o .libs/libdispatch_la-dopaque.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-denum.lo -MD -MP -MF .deps/libdispatch_la-denum.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/denum.c  -fPIC -DPIC -o .libs/libdispatch_la-denum.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-dopaque.lo -MD -MP -MF .deps/libdispatch_la-dopaque.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dopaque.c -o libdispatch_la-dopaque.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-denum.lo -MD -MP -MF .deps/libdispatch_la-denum.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/denum.c -o libdispatch_la-denum.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-dopaque.Tpo .deps/libdispatch_la-dopaque.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libdispatch_la-ncaux.lo -MD -MP -MF .deps/libdispatch_la-ncaux.Tpo -c -o libdispatch_la-ncaux.lo `test -f 'ncaux.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`ncaux.c
    mv -f .deps/libdispatch_la-denum.Tpo .deps/libdispatch_la-denum.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -DDLL_EXPORT -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf2_la-dv2i.lo -MD -MP -MF .deps/libnetcdf2_la-dv2i.Tpo -c -o libnetcdf2_la-dv2i.lo `test -f 'dv2i.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/'`dv2i.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncaux.lo -MD -MP -MF .deps/libdispatch_la-ncaux.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncaux.c  -fPIC -DPIC -o .libs/libdispatch_la-ncaux.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -DDLL_EXPORT -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf2_la-dv2i.lo -MD -MP -MF .deps/libnetcdf2_la-dv2i.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dv2i.c  -fPIC -DPIC -o .libs/libnetcdf2_la-dv2i.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libdispatch_la-ncaux.lo -MD -MP -MF .deps/libdispatch_la-ncaux.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/ncaux.c -o libdispatch_la-ncaux.o >/dev/null 2>&1
    mv -f .deps/libdispatch_la-ncaux.Tpo .deps/libdispatch_la-ncaux.Plo
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o libdispatch.la  libdispatch_la-dparallel.lo libdispatch_la-dcopy.lo libdispatch_la-dfile.lo libdispatch_la-ddim.lo libdispatch_la-datt.lo libdispatch_la-dattinq.lo libdispatch_la-dattput.lo libdispatch_la-dattget.lo libdispatch_la-derror.lo libdispatch_la-dvar.lo libdispatch_la-dvarget.lo libdispatch_la-dvarput.lo libdispatch_la-dvarinq.lo libdispatch_la-ddispatch.lo libdispatch_la-nclog.lo libdispatch_la-dstring.lo libdispatch_la-dutf8proc.lo libdispatch_la-ncuri.lo libdispatch_la-nclist.lo libdispatch_la-ncbytes.lo libdispatch_la-nchashmap.lo libdispatch_la-nctime.lo libdispatch_la-dsubstrate.lo libdispatch_la-nc.lo libdispatch_la-nclistmgr.lo libdispatch_la-dgroup.lo libdispatch_la-dvlen.lo libdispatch_la-dcompound.lo libdispatch_la-dtype.lo libdispatch_la-denum.lo libdispatch_la-dopaque.lo libdispatch_la-ncaux.lo  -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -DDLL_EXPORT -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf2_la-dv2i.lo -MD -MP -MF .deps/libnetcdf2_la-dv2i.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libdispatch/dv2i.c -o libnetcdf2_la-dv2i.o >/dev/null 2>&1
    mv -f .deps/libnetcdf2_la-dv2i.Tpo .deps/libnetcdf2_la-dv2i.Plo
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o libnetcdf2.la  libnetcdf2_la-dv2i.lo  -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: ar cru .libs/libdispatch.a .libs/libdispatch_la-dparallel.o .libs/libdispatch_la-dcopy.o .libs/libdispatch_la-dfile.o .libs/libdispatch_la-ddim.o .libs/libdispatch_la-datt.o .libs/libdispatch_la-dattinq.o .libs/libdispatch_la-dattput.o .libs/libdispatch_la-dattget.o .libs/libdispatch_la-derror.o .libs/libdispatch_la-dvar.o .libs/libdispatch_la-dvarget.o .libs/libdispatch_la-dvarput.o .libs/libdispatch_la-dvarinq.o .libs/libdispatch_la-ddispatch.o .libs/libdispatch_la-nclog.o .libs/libdispatch_la-dstring.o .libs/libdispatch_la-dutf8proc.o .libs/libdispatch_la-ncuri.o .libs/libdispatch_la-nclist.o .libs/libdispatch_la-ncbytes.o .libs/libdispatch_la-nchashmap.o .libs/libdispatch_la-nctime.o .libs/libdispatch_la-dsubstrate.o .libs/libdispatch_la-nc.o .libs/libdispatch_la-nclistmgr.o .libs/libdispatch_la-dgroup.o .libs/libdispatch_la-dvlen.o .libs/libdispatch_la-dcompound.o .libs/libdispatch_la-dtype.o .libs/libdispatch_la-denum.o .libs/libdispatch_la-dopaque.o .libs/libdispatch_la-ncaux.o 
    libtool: link: ranlib .libs/libdispatch.a
    libtool: link: ( cd ".libs" && rm -f "libdispatch.la" && ln -s "../libdispatch.la" "libdispatch.la" )
    libtool: link: ar cru .libs/libnetcdf2.a .libs/libnetcdf2_la-dv2i.o 
    libtool: link: ranlib .libs/libnetcdf2.a
    libtool: link: ( cd ".libs" && rm -f "libnetcdf2.la" && ln -s "../libnetcdf2.la" "libnetcdf2.la" )
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    Making all in libsrc
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-v1hpg.lo -MD -MP -MF .deps/libnetcdf3_la-v1hpg.Tpo -c -o libnetcdf3_la-v1hpg.lo `test -f 'v1hpg.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`v1hpg.c
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-putget.lo -MD -MP -MF .deps/libnetcdf3_la-putget.Tpo -c -o libnetcdf3_la-putget.lo `test -f 'putget.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`putget.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-putget.lo -MD -MP -MF .deps/libnetcdf3_la-putget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/putget.c  -fPIC -DPIC -o .libs/libnetcdf3_la-putget.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-v1hpg.lo -MD -MP -MF .deps/libnetcdf3_la-v1hpg.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/v1hpg.c  -fPIC -DPIC -o .libs/libnetcdf3_la-v1hpg.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-v1hpg.lo -MD -MP -MF .deps/libnetcdf3_la-v1hpg.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/v1hpg.c -o libnetcdf3_la-v1hpg.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-v1hpg.Tpo .deps/libnetcdf3_la-v1hpg.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-attr.lo -MD -MP -MF .deps/libnetcdf3_la-attr.Tpo -c -o libnetcdf3_la-attr.lo `test -f 'attr.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`attr.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-attr.lo -MD -MP -MF .deps/libnetcdf3_la-attr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/attr.c  -fPIC -DPIC -o .libs/libnetcdf3_la-attr.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-attr.lo -MD -MP -MF .deps/libnetcdf3_la-attr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/attr.c -o libnetcdf3_la-attr.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-attr.Tpo .deps/libnetcdf3_la-attr.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-nc3dispatch.lo -MD -MP -MF .deps/libnetcdf3_la-nc3dispatch.Tpo -c -o libnetcdf3_la-nc3dispatch.lo `test -f 'nc3dispatch.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`nc3dispatch.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-nc3dispatch.lo -MD -MP -MF .deps/libnetcdf3_la-nc3dispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/nc3dispatch.c  -fPIC -DPIC -o .libs/libnetcdf3_la-nc3dispatch.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-nc3dispatch.lo -MD -MP -MF .deps/libnetcdf3_la-nc3dispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/nc3dispatch.c -o libnetcdf3_la-nc3dispatch.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-nc3dispatch.Tpo .deps/libnetcdf3_la-nc3dispatch.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-nc3internal.lo -MD -MP -MF .deps/libnetcdf3_la-nc3internal.Tpo -c -o libnetcdf3_la-nc3internal.lo `test -f 'nc3internal.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`nc3internal.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-putget.lo -MD -MP -MF .deps/libnetcdf3_la-putget.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/putget.c -o libnetcdf3_la-putget.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-nc3internal.lo -MD -MP -MF .deps/libnetcdf3_la-nc3internal.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/nc3internal.c  -fPIC -DPIC -o .libs/libnetcdf3_la-nc3internal.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-nc3internal.lo -MD -MP -MF .deps/libnetcdf3_la-nc3internal.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/nc3internal.c -o libnetcdf3_la-nc3internal.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-nc3internal.Tpo .deps/libnetcdf3_la-nc3internal.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-var.lo -MD -MP -MF .deps/libnetcdf3_la-var.Tpo -c -o libnetcdf3_la-var.lo `test -f 'var.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`var.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-var.lo -MD -MP -MF .deps/libnetcdf3_la-var.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/var.c  -fPIC -DPIC -o .libs/libnetcdf3_la-var.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-var.lo -MD -MP -MF .deps/libnetcdf3_la-var.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/var.c -o libnetcdf3_la-var.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-var.Tpo .deps/libnetcdf3_la-var.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-dim.lo -MD -MP -MF .deps/libnetcdf3_la-dim.Tpo -c -o libnetcdf3_la-dim.lo `test -f 'dim.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`dim.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-dim.lo -MD -MP -MF .deps/libnetcdf3_la-dim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/dim.c  -fPIC -DPIC -o .libs/libnetcdf3_la-dim.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-dim.lo -MD -MP -MF .deps/libnetcdf3_la-dim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/dim.c -o libnetcdf3_la-dim.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-putget.Tpo .deps/libnetcdf3_la-putget.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-ncx.lo -MD -MP -MF .deps/libnetcdf3_la-ncx.Tpo -c -o libnetcdf3_la-ncx.lo `test -f 'ncx.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`ncx.c
    mv -f .deps/libnetcdf3_la-dim.Tpo .deps/libnetcdf3_la-dim.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-lookup3.lo -MD -MP -MF .deps/libnetcdf3_la-lookup3.Tpo -c -o libnetcdf3_la-lookup3.lo `test -f 'lookup3.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`lookup3.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-ncx.lo -MD -MP -MF .deps/libnetcdf3_la-ncx.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/ncx.c  -fPIC -DPIC -o .libs/libnetcdf3_la-ncx.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-lookup3.lo -MD -MP -MF .deps/libnetcdf3_la-lookup3.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/lookup3.c  -fPIC -DPIC -o .libs/libnetcdf3_la-lookup3.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-lookup3.lo -MD -MP -MF .deps/libnetcdf3_la-lookup3.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/lookup3.c -o libnetcdf3_la-lookup3.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-lookup3.Tpo .deps/libnetcdf3_la-lookup3.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-ncio.lo -MD -MP -MF .deps/libnetcdf3_la-ncio.Tpo -c -o libnetcdf3_la-ncio.lo `test -f 'ncio.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`ncio.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-ncio.lo -MD -MP -MF .deps/libnetcdf3_la-ncio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/ncio.c  -fPIC -DPIC -o .libs/libnetcdf3_la-ncio.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-ncio.lo -MD -MP -MF .deps/libnetcdf3_la-ncio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/ncio.c -o libnetcdf3_la-ncio.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-ncio.Tpo .deps/libnetcdf3_la-ncio.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-memio.lo -MD -MP -MF .deps/libnetcdf3_la-memio.Tpo -c -o libnetcdf3_la-memio.lo `test -f 'memio.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`memio.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-memio.lo -MD -MP -MF .deps/libnetcdf3_la-memio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/memio.c  -fPIC -DPIC -o .libs/libnetcdf3_la-memio.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-memio.lo -MD -MP -MF .deps/libnetcdf3_la-memio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/memio.c -o libnetcdf3_la-memio.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-memio.Tpo .deps/libnetcdf3_la-memio.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf3_la-posixio.lo -MD -MP -MF .deps/libnetcdf3_la-posixio.Tpo -c -o libnetcdf3_la-posixio.lo `test -f 'posixio.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/'`posixio.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-posixio.lo -MD -MP -MF .deps/libnetcdf3_la-posixio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/posixio.c  -fPIC -DPIC -o .libs/libnetcdf3_la-posixio.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-ncx.lo -MD -MP -MF .deps/libnetcdf3_la-ncx.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/ncx.c -o libnetcdf3_la-ncx.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf3_la-posixio.lo -MD -MP -MF .deps/libnetcdf3_la-posixio.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/posixio.c -o libnetcdf3_la-posixio.o >/dev/null 2>&1
    mv -f .deps/libnetcdf3_la-posixio.Tpo .deps/libnetcdf3_la-posixio.Plo
    m4  -DAPI=C -DNETCDF4=TRUE   /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/docs/netcdf.m4 >netcdf.3  || rm netcdf.3
    mv -f .deps/libnetcdf3_la-ncx.Tpo .deps/libnetcdf3_la-ncx.Plo
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o libnetcdf3.la  libnetcdf3_la-v1hpg.lo libnetcdf3_la-putget.lo libnetcdf3_la-attr.lo libnetcdf3_la-nc3dispatch.lo libnetcdf3_la-nc3internal.lo libnetcdf3_la-var.lo libnetcdf3_la-dim.lo libnetcdf3_la-ncx.lo libnetcdf3_la-lookup3.lo libnetcdf3_la-ncio.lo libnetcdf3_la-memio.lo    libnetcdf3_la-posixio.lo  -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: ar cru .libs/libnetcdf3.a .libs/libnetcdf3_la-v1hpg.o .libs/libnetcdf3_la-putget.o .libs/libnetcdf3_la-attr.o .libs/libnetcdf3_la-nc3dispatch.o .libs/libnetcdf3_la-nc3internal.o .libs/libnetcdf3_la-var.o .libs/libnetcdf3_la-dim.o .libs/libnetcdf3_la-ncx.o .libs/libnetcdf3_la-lookup3.o .libs/libnetcdf3_la-ncio.o .libs/libnetcdf3_la-memio.o .libs/libnetcdf3_la-posixio.o 
    libtool: link: ranlib .libs/libnetcdf3.a
    libtool: link: ( cd ".libs" && rm -f "libnetcdf3.la" && ln -s "../libnetcdf3.la" "libnetcdf3.la" )
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    Making all in libsrc4
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4dispatch.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dispatch.Tpo -c -o libnetcdf4_la-nc4dispatch.lo `test -f 'nc4dispatch.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4dispatch.c
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4attr.lo -MD -MP -MF .deps/libnetcdf4_la-nc4attr.Tpo -c -o libnetcdf4_la-nc4attr.lo `test -f 'nc4attr.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4attr.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4attr.lo -MD -MP -MF .deps/libnetcdf4_la-nc4attr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4attr.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4attr.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4dispatch.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4dispatch.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4dispatch.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4dispatch.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dispatch.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4dispatch.c -o libnetcdf4_la-nc4dispatch.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-nc4dispatch.Tpo .deps/libnetcdf4_la-nc4dispatch.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4dim.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dim.Tpo -c -o libnetcdf4_la-nc4dim.lo `test -f 'nc4dim.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4dim.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4attr.lo -MD -MP -MF .deps/libnetcdf4_la-nc4attr.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4attr.c -o libnetcdf4_la-nc4attr.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4dim.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4dim.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4dim.o
    mv -f .deps/libnetcdf4_la-nc4attr.Tpo .deps/libnetcdf4_la-nc4attr.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4file.lo -MD -MP -MF .deps/libnetcdf4_la-nc4file.Tpo -c -o libnetcdf4_la-nc4file.lo `test -f 'nc4file.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4file.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4dim.lo -MD -MP -MF .deps/libnetcdf4_la-nc4dim.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4dim.c -o libnetcdf4_la-nc4dim.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-nc4dim.Tpo .deps/libnetcdf4_la-nc4dim.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4grp.lo -MD -MP -MF .deps/libnetcdf4_la-nc4grp.Tpo -c -o libnetcdf4_la-nc4grp.lo `test -f 'nc4grp.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4grp.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4file.lo -MD -MP -MF .deps/libnetcdf4_la-nc4file.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4file.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4file.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4grp.lo -MD -MP -MF .deps/libnetcdf4_la-nc4grp.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4grp.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4grp.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4grp.lo -MD -MP -MF .deps/libnetcdf4_la-nc4grp.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4grp.c -o libnetcdf4_la-nc4grp.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-nc4grp.Tpo .deps/libnetcdf4_la-nc4grp.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4hdf.lo -MD -MP -MF .deps/libnetcdf4_la-nc4hdf.Tpo -c -o libnetcdf4_la-nc4hdf.lo `test -f 'nc4hdf.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4hdf.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4file.lo -MD -MP -MF .deps/libnetcdf4_la-nc4file.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4file.c -o libnetcdf4_la-nc4file.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4hdf.lo -MD -MP -MF .deps/libnetcdf4_la-nc4hdf.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4hdf.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4hdf.o
    mv -f .deps/libnetcdf4_la-nc4file.Tpo .deps/libnetcdf4_la-nc4file.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4internal.lo -MD -MP -MF .deps/libnetcdf4_la-nc4internal.Tpo -c -o libnetcdf4_la-nc4internal.lo `test -f 'nc4internal.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4internal.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4internal.lo -MD -MP -MF .deps/libnetcdf4_la-nc4internal.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4internal.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4internal.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4internal.lo -MD -MP -MF .deps/libnetcdf4_la-nc4internal.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4internal.c -o libnetcdf4_la-nc4internal.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4hdf.lo -MD -MP -MF .deps/libnetcdf4_la-nc4hdf.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4hdf.c -o libnetcdf4_la-nc4hdf.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-nc4internal.Tpo .deps/libnetcdf4_la-nc4internal.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4type.lo -MD -MP -MF .deps/libnetcdf4_la-nc4type.Tpo -c -o libnetcdf4_la-nc4type.lo `test -f 'nc4type.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4type.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4type.lo -MD -MP -MF .deps/libnetcdf4_la-nc4type.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4type.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4type.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4type.lo -MD -MP -MF .deps/libnetcdf4_la-nc4type.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4type.c -o libnetcdf4_la-nc4type.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-nc4type.Tpo .deps/libnetcdf4_la-nc4type.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-nc4var.lo -MD -MP -MF .deps/libnetcdf4_la-nc4var.Tpo -c -o libnetcdf4_la-nc4var.lo `test -f 'nc4var.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`nc4var.c
    mv -f .deps/libnetcdf4_la-nc4hdf.Tpo .deps/libnetcdf4_la-nc4hdf.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-ncfunc.lo -MD -MP -MF .deps/libnetcdf4_la-ncfunc.Tpo -c -o libnetcdf4_la-ncfunc.lo `test -f 'ncfunc.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`ncfunc.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4var.lo -MD -MP -MF .deps/libnetcdf4_la-nc4var.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4var.c  -fPIC -DPIC -o .libs/libnetcdf4_la-nc4var.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-nc4var.lo -MD -MP -MF .deps/libnetcdf4_la-nc4var.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/nc4var.c -o libnetcdf4_la-nc4var.o >/dev/null 2>&1
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-ncfunc.lo -MD -MP -MF .deps/libnetcdf4_la-ncfunc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/ncfunc.c  -fPIC -DPIC -o .libs/libnetcdf4_la-ncfunc.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-ncfunc.lo -MD -MP -MF .deps/libnetcdf4_la-ncfunc.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/ncfunc.c -o libnetcdf4_la-ncfunc.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-ncfunc.Tpo .deps/libnetcdf4_la-ncfunc.Plo
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include    -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf4_la-error4.lo -MD -MP -MF .deps/libnetcdf4_la-error4.Tpo -c -o libnetcdf4_la-error4.lo `test -f 'error4.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/'`error4.c
    mv -f .deps/libnetcdf4_la-nc4var.Tpo .deps/libnetcdf4_la-nc4var.Plo
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-error4.lo -MD -MP -MF .deps/libnetcdf4_la-error4.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/error4.c  -fPIC -DPIC -o .libs/libnetcdf4_la-error4.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf4_la-error4.lo -MD -MP -MF .deps/libnetcdf4_la-error4.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4/error4.c -o libnetcdf4_la-error4.o >/dev/null 2>&1
    mv -f .deps/libnetcdf4_la-error4.Tpo .deps/libnetcdf4_la-error4.Plo
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o libnetcdf4.la  libnetcdf4_la-nc4dispatch.lo libnetcdf4_la-nc4attr.lo libnetcdf4_la-nc4dim.lo libnetcdf4_la-nc4file.lo libnetcdf4_la-nc4grp.lo libnetcdf4_la-nc4hdf.lo libnetcdf4_la-nc4internal.lo libnetcdf4_la-nc4type.lo libnetcdf4_la-nc4var.lo libnetcdf4_la-ncfunc.lo libnetcdf4_la-error4.lo  -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: ar cru .libs/libnetcdf4.a .libs/libnetcdf4_la-nc4dispatch.o .libs/libnetcdf4_la-nc4attr.o .libs/libnetcdf4_la-nc4dim.o .libs/libnetcdf4_la-nc4file.o .libs/libnetcdf4_la-nc4grp.o .libs/libnetcdf4_la-nc4hdf.o .libs/libnetcdf4_la-nc4internal.o .libs/libnetcdf4_la-nc4type.o .libs/libnetcdf4_la-nc4var.o .libs/libnetcdf4_la-ncfunc.o .libs/libnetcdf4_la-error4.o 
    libtool: link: ranlib .libs/libnetcdf4.a
    libtool: link: ( cd ".libs" && rm -f "libnetcdf4.la" && ln -s "../libnetcdf4.la" "libnetcdf4.la" )
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    Making all in liblib
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
    /bin/sh ../libtool  --tag=CC   --mode=compile gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include     -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT libnetcdf_la-nc_initialize.lo -MD -MP -MF .deps/libnetcdf_la-nc_initialize.Tpo -c -o libnetcdf_la-nc_initialize.lo `test -f 'nc_initialize.c' || echo '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib/'`nc_initialize.c
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf_la-nc_initialize.lo -MD -MP -MF .deps/libnetcdf_la-nc_initialize.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib/nc_initialize.c  -fPIC -DPIC -o .libs/libnetcdf_la-nc_initialize.o
    libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib -I.. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc4 -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include -g -O2 -MT libnetcdf_la-nc_initialize.lo -MD -MP -MF .deps/libnetcdf_la-nc_initialize.Tpo -c /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/liblib/nc_initialize.c -o libnetcdf_la-nc_initialize.o >/dev/null 2>&1
    mv -f .deps/libnetcdf_la-nc_initialize.Tpo .deps/libnetcdf_la-nc_initialize.Plo
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2 -version-info 11:0:0   -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o libnetcdf.la -rpath /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib libnetcdf_la-nc_initialize.lo ../libdispatch/libnetcdf2.la ../libdispatch/libdispatch.la ../libsrc/libnetcdf3.la   ../libsrc4/libnetcdf4.la -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: gcc -shared  -fPIC -DPIC  .libs/libnetcdf_la-nc_initialize.o  -Wl,--whole-archive ../libdispatch/.libs/libnetcdf2.a ../libdispatch/.libs/libdispatch.a ../libsrc/.libs/libnetcdf3.a ../libsrc4/.libs/libnetcdf4.a -Wl,--no-whole-archive  -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so -ldl -lm -lz  -g -O2   -Wl,-soname -Wl,libnetcdf.so.11 -o .libs/libnetcdf.so.11.0.0
    libtool: link: (cd ".libs" && rm -f "libnetcdf.so.11" && ln -s "libnetcdf.so.11.0.0" "libnetcdf.so.11")
    libtool: link: (cd ".libs" && rm -f "libnetcdf.so" && ln -s "libnetcdf.so.11.0.0" "libnetcdf.so")
    libtool: link: (cd .libs/libnetcdf.lax/libnetcdf2.a && ar x "/gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib/../libdispatch/.libs/libnetcdf2.a")
    libtool: link: (cd .libs/libnetcdf.lax/libdispatch.a && ar x "/gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib/../libdispatch/.libs/libdispatch.a")
    libtool: link: (cd .libs/libnetcdf.lax/libnetcdf3.a && ar x "/gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib/../libsrc/.libs/libnetcdf3.a")
    libtool: link: (cd .libs/libnetcdf.lax/libnetcdf4.a && ar x "/gpfs/fs01/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib/../libsrc4/.libs/libnetcdf4.a")
    libtool: link: ar cru .libs/libnetcdf.a  libnetcdf_la-nc_initialize.o  .libs/libnetcdf.lax/libnetcdf2.a/libnetcdf2_la-dv2i.o  .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-datt.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dattget.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dattinq.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dattput.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dcompound.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dcopy.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-ddim.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-ddispatch.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-denum.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-derror.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dfile.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dgroup.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dopaque.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dparallel.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dstring.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dsubstrate.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dtype.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dutf8proc.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dvar.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dvarget.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dvarinq.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dvarput.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-dvlen.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nc.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-ncaux.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-ncbytes.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nchashmap.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nclist.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nclistmgr.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nclog.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-nctime.o .libs/libnetcdf.lax/libdispatch.a/libdispatch_la-ncuri.o  .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-attr.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-dim.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-lookup3.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-memio.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-nc3dispatch.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-nc3internal.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-ncio.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-ncx.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-posixio.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-putget.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-v1hpg.o .libs/libnetcdf.lax/libnetcdf3.a/libnetcdf3_la-var.o  .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-error4.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4attr.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4dim.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4dispatch.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4file.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4grp.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4hdf.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4internal.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4type.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-nc4var.o .libs/libnetcdf.lax/libnetcdf4.a/libnetcdf4_la-ncfunc.o 
    libtool: link: ranlib .libs/libnetcdf.a
    libtool: link: rm -fr .libs/libnetcdf.lax
    libtool: link: ( cd ".libs" && rm -f "libnetcdf.la" && ln -s "../libnetcdf.la" "libnetcdf.la" )
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
    Making all in ncgen3
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
    depbase=`echo main.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT main.o -MD -MP -MF $depbase.Tpo -c -o main.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/main.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo load.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT load.o -MD -MP -MF $depbase.Tpo -c -o load.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/load.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo escapes.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT escapes.o -MD -MP -MF $depbase.Tpo -c -o escapes.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/escapes.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo getfill.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT getfill.o -MD -MP -MF $depbase.Tpo -c -o getfill.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/getfill.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo init.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT init.o -MD -MP -MF $depbase.Tpo -c -o init.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/init.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genlib.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genlib.o -MD -MP -MF $depbase.Tpo -c -o genlib.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/genlib.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo ncgentab.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3 -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT ncgentab.o -MD -MP -MF $depbase.Tpo -c -o ncgentab.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/ncgentab.c &&\
    mv -f $depbase.Tpo $depbase.Po
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o ncgen3 main.o load.o escapes.o getfill.o init.o genlib.o ncgentab.o ../liblib/libnetcdf.la  -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: gcc -g -O2 -o .libs/ncgen3 main.o load.o escapes.o getfill.o init.o genlib.o ncgentab.o  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib ../liblib/.libs/libnetcdf.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so -ldl -lm -lz -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
    Making all in ncgen
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
    depbase=`echo generate.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT generate.o -MD -MP -MF $depbase.Tpo -c -o generate.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/generate.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo main.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT main.o -MD -MP -MF $depbase.Tpo -c -o main.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/main.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo cdata.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT cdata.o -MD -MP -MF $depbase.Tpo -c -o cdata.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/cdata.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo bindata.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT bindata.o -MD -MP -MF $depbase.Tpo -c -o bindata.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/bindata.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genchar.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genchar.o -MD -MP -MF $depbase.Tpo -c -o genchar.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genchar.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo cvt.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT cvt.o -MD -MP -MF $depbase.Tpo -c -o cvt.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/cvt.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo data.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT data.o -MD -MP -MF $depbase.Tpo -c -o data.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/data.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo debug.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT debug.o -MD -MP -MF $depbase.Tpo -c -o debug.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/debug.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo escapes.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT escapes.o -MD -MP -MF $depbase.Tpo -c -o escapes.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/escapes.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genc.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genc.o -MD -MP -MF $depbase.Tpo -c -o genc.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genc.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genbin.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genbin.o -MD -MP -MF $depbase.Tpo -c -o genbin.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genbin.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo generr.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT generr.o -MD -MP -MF $depbase.Tpo -c -o generr.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/generr.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genlib.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genlib.o -MD -MP -MF $depbase.Tpo -c -o genlib.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genlib.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo getfill.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT getfill.o -MD -MP -MF $depbase.Tpo -c -o getfill.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/getfill.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo odom.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT odom.o -MD -MP -MF $depbase.Tpo -c -o odom.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/odom.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo offsets.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT offsets.o -MD -MP -MF $depbase.Tpo -c -o offsets.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/offsets.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo semantics.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT semantics.o -MD -MP -MF $depbase.Tpo -c -o semantics.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/semantics.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo dump.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT dump.o -MD -MP -MF $depbase.Tpo -c -o dump.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/dump.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo util.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT util.o -MD -MP -MF $depbase.Tpo -c -o util.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/util.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo bytebuffer.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT bytebuffer.o -MD -MP -MF $depbase.Tpo -c -o bytebuffer.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/bytebuffer.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo list.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT list.o -MD -MP -MF $depbase.Tpo -c -o list.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/list.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genf77.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genf77.o -MD -MP -MF $depbase.Tpo -c -o genf77.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genf77.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo f77data.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT f77data.o -MD -MP -MF $depbase.Tpo -c -o f77data.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/f77data.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo genj.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT genj.o -MD -MP -MF $depbase.Tpo -c -o genj.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/genj.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo jdata.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT jdata.o -MD -MP -MF $depbase.Tpo -c -o jdata.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/jdata.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo nc_iter.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT nc_iter.o -MD -MP -MF $depbase.Tpo -c -o nc_iter.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/nc_iter.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo ConvertUTF.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT ConvertUTF.o -MD -MP -MF $depbase.Tpo -c -o ConvertUTF.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/ConvertUTF.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo ncgeny.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT ncgeny.o -MD -MP -MF $depbase.Tpo -c -o ncgeny.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/ncgeny.c &&\
    mv -f $depbase.Tpo $depbase.Po
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o ncgen generate.o main.o cdata.o bindata.o genchar.o cvt.o data.o debug.o escapes.o genc.o genbin.o generr.o genlib.o getfill.o odom.o offsets.o semantics.o dump.o util.o bytebuffer.o list.o genf77.o f77data.o genj.o jdata.o nc_iter.o ConvertUTF.o ncgeny.o ../liblib/libnetcdf.la -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: gcc -g -O2 -o .libs/ncgen generate.o main.o cdata.o bindata.o genchar.o cvt.o data.o debug.o escapes.o genc.o genbin.o generr.o genlib.o getfill.o odom.o offsets.o semantics.o dump.o util.o bytebuffer.o list.o genf77.o f77data.o genj.o jdata.o nc_iter.o ConvertUTF.o ncgeny.o  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib ../liblib/.libs/libnetcdf.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so -ldl -lm -lz -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
    Making all in ncdump
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    ../ncgen/ncgen -lc -o ctest0.nc /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/c0.cdl > ../ncdump/ctest.c
    ../ncgen/ncgen -k2 -lc -o ctest0_64.nc /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/c0.cdl > /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/ctest64.c
    make  all-recursive
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    Making all in cdl
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    make[4]: Nothing to be done for `all'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    Making all in expected
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[4]: Nothing to be done for `all'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    depbase=`echo ncdump.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT ncdump.o -MD -MP -MF $depbase.Tpo -c -o ncdump.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/ncdump.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo vardata.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT vardata.o -MD -MP -MF $depbase.Tpo -c -o vardata.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/vardata.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo dumplib.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT dumplib.o -MD -MP -MF $depbase.Tpo -c -o dumplib.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/dumplib.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo indent.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT indent.o -MD -MP -MF $depbase.Tpo -c -o indent.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/indent.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo nctime0.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT nctime0.o -MD -MP -MF $depbase.Tpo -c -o nctime0.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/nctime0.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo utils.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT utils.o -MD -MP -MF $depbase.Tpo -c -o utils.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/utils.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo nciter.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT nciter.o -MD -MP -MF $depbase.Tpo -c -o nciter.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/nciter.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo nccopy.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT nccopy.o -MD -MP -MF $depbase.Tpo -c -o nccopy.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/nccopy.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo chunkspec.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT chunkspec.o -MD -MP -MF $depbase.Tpo -c -o chunkspec.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/chunkspec.c &&\
    mv -f $depbase.Tpo $depbase.Po
    depbase=`echo dimmap.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
    gcc -DHAVE_CONFIG_H -I. -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump -I..  -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include   -I/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/include  -g -O2 -MT dimmap.o -MD -MP -MF $depbase.Tpo -c -o dimmap.o /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/dimmap.c &&\
    mv -f $depbase.Tpo $depbase.Po
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o ncdump ncdump.o vardata.o dumplib.o indent.o nctime0.o utils.o nciter.o ../liblib/libnetcdf.la -lhdf5_hl -lhdf5 -ldl -lm -lz 
    /bin/sh ../libtool  --tag=CC   --mode=link gcc  -g -O2  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib -o nccopy nccopy.o nciter.o chunkspec.o utils.o dimmap.o ../liblib/libnetcdf.la -lhdf5_hl -lhdf5 -ldl -lm -lz 
    libtool: link: gcc -g -O2 -o .libs/ncdump ncdump.o vardata.o dumplib.o indent.o nctime0.o utils.o nciter.o  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib ../liblib/.libs/libnetcdf.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so -ldl -lm -lz -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    libtool: link: gcc -g -O2 -o .libs/nccopy nccopy.o nciter.o chunkspec.o utils.o dimmap.o  -L/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib ../liblib/.libs/libnetcdf.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5_hl.so /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib/libhdf5.so -ldl -lm -lz -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib -Wl,-rpath -Wl,/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_hdf5/hdf5/lib
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    Making all in nctest
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    Making all in nc_test
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    cp /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/libsrc/ncx.c .
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    Making all in nc_test4
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    Making all in docs
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    Making all in images
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[3]: Nothing to be done for `all'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[3]: Nothing to be done for `all-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    Making all in examples
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    Making all in C
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    make[3]: Nothing to be done for `all'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    Making all in CDL
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[3]: Nothing to be done for `all'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[3]: Nothing to be done for `all-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    Making install in include
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    make[2]: Nothing to be done for `install-exec-am'.
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/include'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include/netcdf.h netcdf_meta.h /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/include/netcdf_mem.h '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/include'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/include'
    Making install in h5_test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/h5_test'
    Making install in libdispatch
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libdispatch'
    Making install in libsrc
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    make[2]: Nothing to be done for `install-exec-am'.
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man3'
     /bin/install -c -m 644 netcdf.3 '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man3'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc'
    Making install in libsrc4
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/libsrc4'
    Making install in liblib
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib'
     /bin/sh ../libtool   --mode=install /bin/install -c   libnetcdf.la '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib'
    libtool: install: /bin/install -c .libs/libnetcdf.so.11.0.0 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/libnetcdf.so.11.0.0
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib && { ln -s -f libnetcdf.so.11.0.0 libnetcdf.so.11 || { rm -f libnetcdf.so.11 && ln -s libnetcdf.so.11.0.0 libnetcdf.so.11; }; })
    libtool: install: (cd /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib && { ln -s -f libnetcdf.so.11.0.0 libnetcdf.so || { rm -f libnetcdf.so && ln -s libnetcdf.so.11.0.0 libnetcdf.so; }; })
    libtool: install: /bin/install -c .libs/libnetcdf.lai /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/libnetcdf.la
    libtool: install: /bin/install -c .libs/libnetcdf.a /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/libnetcdf.a
    libtool: install: chmod 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/libnetcdf.a
    libtool: install: ranlib /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/libnetcdf.a
    libtool: finish: PATH="/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/system/bin:/usr/local/src/bluemix_jupyter_bundle.v4/notebook/freetype/bin:/bin:/sbin" ldconfig -n /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib
    
    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the '-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the 'LD_RUN_PATH' environment variable
         during linking
       - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to '/etc/ld.so.conf'
    
    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/liblib'
    Making install in ncgen3
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
      /bin/sh ../libtool   --mode=install /bin/install -c ncgen3 '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
    libtool: install: /bin/install -c .libs/ncgen3 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin/ncgen3
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen3/ncgen3.1 '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen3'
    Making install in ncgen
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
      /bin/sh ../libtool   --mode=install /bin/install -c ncgen '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
    libtool: install: /bin/install -c .libs/ncgen /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin/ncgen
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncgen/ncgen.1 '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncgen'
    Making install in ncdump
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make  install-recursive
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    Making install in cdl
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    make[4]: Nothing to be done for `install-exec-am'.
    make[4]: Nothing to be done for `install-data-am'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/cdl'
    Making install in expected
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[4]: Nothing to be done for `install-exec-am'.
    make[4]: Nothing to be done for `install-data-am'.
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump/expected'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[4]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
      /bin/sh ../libtool   --mode=install /bin/install -c ncdump nccopy '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
    libtool: install: /bin/install -c .libs/ncdump /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin/ncdump
    libtool: install: /bin/install -c .libs/nccopy /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin/nccopy
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
     /bin/install -c -m 644 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/ncdump.1 /gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf-c-4.4.0/ncdump/nccopy.1 '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/share/man/man1'
    make[4]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/ncdump'
    Making install in nctest
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nctest'
    Making install in nc_test
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test'
    Making install in nc_test4
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    make[2]: Nothing to be done for `install-exec-am'.
    make[2]: Nothing to be done for `install-data-am'.
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/nc_test4'
    Making install in docs
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    Making install in images
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs/images'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/docs'
    Making install in examples
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    Making install in C
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/C'
    Making install in CDL
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples/CDL'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[3]: Nothing to be done for `install-exec-am'.
    make[3]: Nothing to be done for `install-data-am'.
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work/examples'
    make[1]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[2]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
     /bin/install -c nc-config '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/bin'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/pkgconfig'
     /bin/install -c -m 644 netcdf.pc '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib/pkgconfig'
     /bin/mkdir -p '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib'
     /bin/install -c -m 644 libnetcdf.settings '/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/.local/share/notebook_netcdf/netcdf/lib'
    make  install-data-hook
    make[3]: Entering directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    
    +-------------------------------------------------------------+
    | Congratulations! You have successfully installed netCDF!    |
    |                                                             |
    | You can use script "nc-config" to find out the relevant     |
    | compiler options to build your application. Enter           |
    |                                                             |
    |     nc-config --help                                        |
    |                                                             |
    | for additional information.                                 |
    |                                                             |
    | CAUTION:                                                    |
    |                                                             |
    | If you have not already run "make check", then we strongly  |
    | recommend you do so. It does not take very long.            |
    |                                                             |
    | Before using netCDF to store important data, test your      |
    | build with "make check".                                    |
    |                                                             |
    | NetCDF is tested nightly on many platforms at Unidata       |
    | but your platform is probably different in some ways.       |
    |                                                             |
    | If any tests fail, please see the netCDF web site:          |
    | http://www.unidata.ucar.edu/software/netcdf/                |
    |                                                             |
    | NetCDF is developed and maintained at the Unidata Program   |
    | Center. Unidata provides a broad array of data and software |
    | tools for use in geoscience education and research.         |
    | http://www.unidata.ucar.edu                                 |
    +-------------------------------------------------------------+
    
    make[3]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[2]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make[1]: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'
    make: Leaving directory `/gpfs/global_fs01/sym_shared/YPProdSpark/user/s1a2-472d95bcebf7db-bf066087ecf5/notebook/work'


<div class="alert alert-danger" role="alert">
  <strong>WARNING</strong> You may have to reload your browser to continue.  The Make and Make Install process can sometimes cause the notebook to lose it's kernel context.  Don't worry, all of the compiled files will persist across the browser reload.
</div>

## Housekeeping


```python
# Re-evaluate installation status
isNETCDFInstalled = os.path.isfile(netcdfDir + "/netcdf/bin/nc-config")

if isNETCDFInstalled:
    # Remove all of configure generated by-products
    !rm -rf * 2>/dev/null
    # Remove the extracted source folder
    !rm -rf $netcdfDir/netcdf-c-4.4.0 2>/dev/null
    # Remove the tar gzip file
    !rm $netcdfDir/v4.4.0.tar.gz 2>/dev/null
    print "NETCDF directories for facilitating NETCDF build and install have been removed"
else:
    print "NETCDF build and install seems to have failed.  Cannot local <NETCDF_Install_Point>/bin/nc-config"
```

    NETCDF directories for facilitating NETCDF build and install have been removed


<div class="alert alert-info" role="alert">
  <strong>Prequisite Native Library Installs Complete</strong> Let's now do some pip installs
</div>

### Setting up custom paths via Environment Variables


```python
if isHDF5Installed:
    os.environ['HDF5_DIR']= hdf5Dir + "/hdf5"
    os.environ['HDF5_DIR_INCDIR']= hdf5Dir + "/hdf5/inc"
    os.environ['HDF5_DIR_LIBDIR']= hdf5Dir + "/hdf5/lib"
    os.environ['PKG_CONFIG_PATH']= hdf5Dir + "/hdf5" 

if isNETCDFInstalled:
    os.environ['NETCDF4_DIR']= netcdfDir + "/netcdf"
    os.environ['NETCDF4_INCDIR']= netcdfDir + "/netcdf/inc"
    os.environ['NETCDF4_LIBDIR']= netcdfDir + "/netcdf/lib" 
    os.environ['PKG_CONFIG_PATH']= netcdfDir + "/netcdf"
    
if isHDF5Installed and isNETCDFInstalled:
    os.environ['PKG_CONFIG_PATH']= netcdfDir + "/netcdf" + ":" + hdf5Dir + "/hdf5"   # Needed for NETCDF4 PIP Install
    os.environ['USE_NCCONFIG']= "1"  # Needed for NETCDF4 PIP Install
    os.environ['USE_SETUPCFG']= "0"  # Needed for NETCDF4 PIP Install
```

### Installing pkgconfig Python module :: interface with the pkg-config command line tool 


```python
!pip install pkgconfig --user
```

    Collecting pkgconfig
      Using cached pkgconfig-1.1.0.tar.gz
    Installing collected packages: pkgconfig
      Running setup.py install for pkgconfig ... [?25l- done
    [?25hSuccessfully installed pkgconfig-1.1.0


### Installing h5py Python module :: The h5py package provides both a high- and low-level interface to the HDF5 library from Python. The low-level interface is intended to be a complete wrapping of the HDF5 API, while the high-level component supports access to HDF5 files, datasets and groups using established Python and NumPy concepts.


```python
if isHDF5Installed:
    !pip install h5py --user
```

    Collecting h5py
      Using cached h5py-2.6.0.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): numpy>=1.6.1 in /usr/local/src/bluemix_jupyter_bundle.v4/notebook/lib/python2.7/site-packages (from h5py)
    Requirement already satisfied (use --upgrade to upgrade): six in /usr/local/src/bluemix_jupyter_bundle.v4/notebook/lib/python2.7/site-packages (from h5py)
    Installing collected packages: h5py
      Running setup.py install for h5py ... [?25l- \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ | / - \ done
    [?25hSuccessfully installed h5py-2.6.0


### Installing NETCDF4 Python module :: netCDF version 4 has many features not found in earlier versions of the library and is implemented on top of HDF5. This module can read and write files in both the new netCDF 4 and the old netCDF 3 format, and can create files that are readable by HDF5 clients. The API modelled after Scientific.IO.NetCDF, and should be familiar to users of that module.

<div class="alert alert-info" role="alert">
  The NETCDF4 module pip installation requires context on the install locations of HDF5 and NETCDF.  
  Given that it uses the pkg-config process, we must create 2 *.pc files that outline their installation location.
  <ol><li>hdf5.pc</li>
  <li>netcfd.pc</li></ol>
</div>

### Create HDF5.pc file


```python
!echo "# An example pkg-config file for hdf5.  Fix the paths if necessary and put" > hdf5.pc
!echo "# this in a place where pkg-config will find it.  You can find where it's" >> hdf5.pc
!echo "# searching now with the following command:" >> hdf5.pc
!echo "#" >> hdf5.pc
!echo "#   $ pkg-config --variable pc_path pkg-config" >> hdf5.pc
!echo "#" >> hdf5.pc
!echo "# According to the man page, you can override this if necessary by" >> hdf5.pc
!echo "# setting PKG_CONFIG_PATH" >> hdf5.pc
!echo "#" >> hdf5.pc
!echo "" >> hdf5.pc
!echo "prefix=${HDF5_DIR}" >> hdf5.pc
!echo "exec_prefix=${HDF5_DIR}" >> hdf5.pc
!echo "includedir=${HDF5_DIR}/include" >> hdf5.pc
!echo "libdir=${HDF5_DIR}/lib" >> hdf5.pc
!echo "" >> hdf5.pc
!echo "Name: hdf5" >> hdf5.pc
!echo "Description: HDF5" >> hdf5.pc
!echo "Version: 1.8.17" >> hdf5.pc
!echo "Requires.private: zlib" >> hdf5.pc
!echo "Cflags: -I${HDF5_DIR}/include" >> hdf5.pc
!echo "Libs: -L${HDF5_DIR}/lib -lhdf5" >> hdf5.pc
```

### Move the HDF5.pc file into the HDF5 install location


```python
!mv hdf5.pc $hdf5Dir/hdf5
```

### Create NETCDF.pc file


```python
!echo "# An example pkg-config file for netcdf.  Fix the paths if necessary and put" > netcdf.pc
!echo "# this in a place where pkg-config will find it.  You can find where it's" >> netcdf.pc
!echo "# searching now with the following command:" >> netcdf.pc
!echo "#" >> netcdf.pc
!echo "#   $ pkg-config --variable pc_path pkg-config" >> netcdf.pc
!echo "#" >> netcdf.pc
!echo "# According to the man page, you can override this if necessary by" >> netcdf.pc
!echo "# setting PKG_CONFIG_PATH" >> netcdf.pc
!echo "#" >> netcdf.pc
!echo "" >> netcdf.pc
!echo "prefix=${NETCDF4_DIR}" >> netcdf.pc
!echo "exec_prefix=${NETCDF4_DIR}" >> netcdf.pc
!echo "includedir=${NETCDF4_DIR}/include" >> netcdf.pc
!echo "libdir=${NETCDF4_DIR}/lib" >> netcdf.pc
!echo "ccompiler=gcc" >> netcdf.pc

!echo "" >> netcdf.pc
!echo "Name: netcdf" >> netcdf.pc
!echo "Description: NetCDF Client Library" >> netcdf.pc
!echo "URL: http://www.unidata.ucar.edu/netcdf" >> netcdf.pc
!echo "Version: 4.4.0" >> netcdf.pc
!echo "Requires.private: zlib" >> hdf5.pc
!echo "Cflags: -I${NETCDF4_DIR}/include" >> netcdf.pc
!echo "Libs: -L${NETCDF4_DIR}/lib -lnetcdf" >> netcdf.pc
```

### Move the NETCDF.pc file into the NETCDF install location


```python
!mv netcdf.pc $netcdfDir/netcdf
```


```python
if isHDF5Installed and isNETCDFInstalled:
    !pip install netCDF4 --user
```

    Collecting netCDF4
      Using cached netCDF4-1.2.4.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): numpy>=1.7 in /usr/local/src/bluemix_jupyter_bundle.v4/notebook/lib/python2.7/site-packages (from netCDF4)
    Installing collected packages: netCDF4
      Running setup.py install for netCDF4 ... [?25l- \ | / - \ | / - \ | done
    [?25hSuccessfully installed netCDF4-1.2.4


<div class="alert alert-success" role="alert">
  <strong>Congratulations</strong> You went through the wormhole and made it to the other side.  
  Let's review our accomplishments:
  <ol><li>HDF5 Native Lib Installed .............................   Check</li>
      <li>NETCDF Native Lib Installed ...........................   Check</li>
      <li>Python Module Cython Installed ........................   Check</li>
      <li>Python Module H5PY Installed ..........................   Check</li>
      <li>Python Module Numpy Installed .........................   Check</li>
      <li>Python Module NETCDF4 with HDF5 support Installed .....   Check</li>
      <li>Python Module pkg-config Installed ....................   Check</li></ol>
  
  Whew!
</div>

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
f.close()
```

# Let's work through a few netCDF4 commands


```python
!$netcdfDir/netcdf/bin/nc-config --version  # --cflags
```

    netCDF 4.4.0



```python
# standard imports
import netCDF4 as nc
import numpy as np
from netCDF4 import Dataset
np.set_printoptions(precision=3, linewidth=100, edgeitems=2)  # make numpy less verbose
nc.getlibversion()
```




    u'4.4.0 of May 23 2016 00:44:04 $'




```python
rootgrp = Dataset("test.nc", "w", format="NETCDF4")
print rootgrp.data_model
rootgrp.close()
```

    NETCDF4



```python
rootgrp = Dataset("test.nc", "a")
fcstgrp = rootgrp.createGroup("forecasts")
analgrp = rootgrp.createGroup("analyses")
print rootgrp.groups
```

    OrderedDict([('forecasts', <type 'netCDF4._netCDF4.Group'>
    group /forecasts:
        dimensions(sizes): 
        variables(dimensions): 
        groups: 
    ), ('analyses', <type 'netCDF4._netCDF4.Group'>
    group /analyses:
        dimensions(sizes): 
        variables(dimensions): 
        groups: 
    )])



```python
fcstgrp1 = rootgrp.createGroup("/forecasts/model1")
fcstgrp2 = rootgrp.createGroup("/forecasts/model2")
```


```python
def walktree(top):
    values = top.groups.values()
    yield values
    for value in top.groups.values():
        for children in walktree(value):
            yield children

print rootgrp
for children in walktree(rootgrp):
    for child in children:
        print child
```

    <type 'netCDF4._netCDF4.Dataset'>
    root group (NETCDF4 data model, file format HDF5):
        dimensions(sizes): 
        variables(dimensions): 
        groups: forecasts, analyses
    
    <type 'netCDF4._netCDF4.Group'>
    group /forecasts:
        dimensions(sizes): 
        variables(dimensions): 
        groups: model1, model2
    
    <type 'netCDF4._netCDF4.Group'>
    group /analyses:
        dimensions(sizes): 
        variables(dimensions): 
        groups: 
    
    <type 'netCDF4._netCDF4.Group'>
    group /forecasts/model1:
        dimensions(sizes): 
        variables(dimensions): 
        groups: 
    
    <type 'netCDF4._netCDF4.Group'>
    group /forecasts/model2:
        dimensions(sizes): 
        variables(dimensions): 
        groups: 
    



```python
level = rootgrp.createDimension("level", None)
time = rootgrp.createDimension("time", None)
lat = rootgrp.createDimension("lat", 73)
lon = rootgrp.createDimension("lon", 144)
print rootgrp.dimensions
```

    OrderedDict([('level', <type 'netCDF4._netCDF4.Dimension'> (unlimited): name = 'level', size = 0
    ), ('time', <type 'netCDF4._netCDF4.Dimension'> (unlimited): name = 'time', size = 0
    ), ('lat', <type 'netCDF4._netCDF4.Dimension'>: name = 'lat', size = 73
    ), ('lon', <type 'netCDF4._netCDF4.Dimension'>: name = 'lon', size = 144
    )])



```python
print len(lon)
print lon.isunlimited()
print time.isunlimited()
```

    144
    False
    True



```python
for dimobj in rootgrp.dimensions.values():
    print dimobj
```

    <type 'netCDF4._netCDF4.Dimension'> (unlimited): name = 'level', size = 0
    
    <type 'netCDF4._netCDF4.Dimension'> (unlimited): name = 'time', size = 0
    
    <type 'netCDF4._netCDF4.Dimension'>: name = 'lat', size = 73
    
    <type 'netCDF4._netCDF4.Dimension'>: name = 'lon', size = 144
    



```python
times = rootgrp.createVariable("time","f8",("time",))
levels = rootgrp.createVariable("level","i4",("level",))
latitudes = rootgrp.createVariable("latitude","f4",("lat",))
longitudes = rootgrp.createVariable("longitude","f4",("lon"))
temp = rootgrp.createVariable("temp","f4",("time","level","lat","lon",))
print temp
```

    <type 'netCDF4._netCDF4.Variable'>
    float32 temp(time, level, lat, lon)
    unlimited dimensions: time, level
    current shape = (0, 0, 73, 144)
    filling on, default _FillValue of 9.96920996839e+36 used
    



```python
ftemp = rootgrp.createVariable("/forecasts/model1/temp","f4",("time","level","lat","lon",))
```


```python
print rootgrp["/forecasts/model1"]
print rootgrp["/forecasts/model1/temp"]
```

    <type 'netCDF4._netCDF4.Group'>
    group /forecasts/model1:
        dimensions(sizes): 
        variables(dimensions): float32 [4mtemp[0m(time,level,lat,lon)
        groups: 
    
    <type 'netCDF4._netCDF4.Variable'>
    float32 temp(time, level, lat, lon)
    path = /forecasts/model1
    unlimited dimensions: time, level
    current shape = (0, 0, 73, 144)
    filling on, default _FillValue of 9.96920996839e+36 used
    



```python
print rootgrp.variables
```

    OrderedDict([('time', <type 'netCDF4._netCDF4.Variable'>
    float64 time(time)
    unlimited dimensions: time
    current shape = (0,)
    filling on, default _FillValue of 9.96920996839e+36 used
    ), ('level', <type 'netCDF4._netCDF4.Variable'>
    int32 level(level)
    unlimited dimensions: level
    current shape = (0,)
    filling on, default _FillValue of -2147483647 used
    ), ('latitude', <type 'netCDF4._netCDF4.Variable'>
    float32 latitude(lat)
    unlimited dimensions: 
    current shape = (73,)
    filling on, default _FillValue of 9.96920996839e+36 used
    ), ('longitude', <type 'netCDF4._netCDF4.Variable'>
    float32 longitude(lon)
    unlimited dimensions: 
    current shape = (144,)
    filling on, default _FillValue of 9.96920996839e+36 used
    ), ('temp', <type 'netCDF4._netCDF4.Variable'>
    float32 temp(time, level, lat, lon)
    unlimited dimensions: time, level
    current shape = (0, 0, 73, 144)
    filling on, default _FillValue of 9.96920996839e+36 used
    )])



```python
import time
rootgrp.description = "bogus example script"
rootgrp.history = "Created " + time.ctime(time.time())
rootgrp.source = "netCDF4 python module tutorial from unidata.github.io"
latitudes.units = "degrees north"
longitudes.units = "degrees east"
levels.units = "hPa"
temp.units = "K"
times.units = "hours since 0001-01-01 00:00:00.0"
times.calendar = "gregorian"
```


```python
for name in rootgrp.ncattrs():
    print "Global attr", name, "=", getattr(rootgrp,name)
```

    Global attr description = bogus example script
    Global attr history = Created Mon May 23 00:58:27 2016
    Global attr source = netCDF4 python module tutorial from unidata.github.io



```python
print rootgrp.__dict__
```

    OrderedDict([(u'description', u'bogus example script'), (u'history', u'Created Mon May 23 00:58:27 2016'), (u'source', u'netCDF4 python module tutorial from unidata.github.io')])



```python
lats = np.arange(-90,91,2.5)
lons = np.arange(-180,180,2.5)
latitudes[:] = lats
longitudes[:] = lons
print "latitudes =\n",latitudes[:]
```

    latitudes =
    [-90.  -87.5 -85.  -82.5 -80.  -77.5 -75.  -72.5 -70.  -67.5 -65.  -62.5 -60.  -57.5 -55.  -52.5
     -50.  -47.5 -45.  -42.5 -40.  -37.5 -35.  -32.5 -30.  -27.5 -25.  -22.5 -20.  -17.5 -15.  -12.5
     -10.   -7.5  -5.   -2.5   0.    2.5   5.    7.5  10.   12.5  15.   17.5  20.   22.5  25.   27.5
      30.   32.5  35.   37.5  40.   42.5  45.   47.5  50.   52.5  55.   57.5  60.   62.5  65.   67.5
      70.   72.5  75.   77.5  80.   82.5  85.   87.5  90. ]



```python
#append along two unlimited dimensions by assign to slice
nlats = len(rootgrp.dimensions["lat"])
nlons = len(rootgrp.dimensions["lon"])
print "temp shape before adding data = ", temp.shape
```

    temp shape before adding data =  (0, 0, 73, 144)



```python
from numpy.random import uniform
temp[0:5,0:10,:,:] = uniform(size=(5,10,nlats,nlons))
print "temp shape after adding data = ",temp.shape
```

    temp shape after adding data =  (5, 10, 73, 144)



```python
# levels have grown, but no values yet assigned
print "levels shape after adding pressure data = ", levels.shape
```

    levels shape after adding pressure data =  (10,)



```python
# now, assign data to levels dimension variable
levels[:] = [1000.,850.,700.,500.,300.,250.,200.,150.,10.,50.]
temp[0, 0, [0,1,2,3], [0,1,2,3]]
tempdat = temp[::2, [1,3,6], lats>0, lons>0]
print "shape of fancy temp slice = ", tempdat.shape
```

    shape of fancy temp slice =  (3, 3, 36, 71)



```python
# fill in times
from datetime import datetime, timedelta
from netCDF4 import num2date, date2num
dates = [datetime(2001,3,1)+n*timedelta(hours=12) for n in range(temp.shape[0])]
times[:] = date2num(dates, units=times.units,calendar=times.calendar)
print "time values (in units %s): " % times.units+"\n",times[:]
```

    time values (in units hours since 0001-01-01 00:00:00.0): 
    [ 17533104.  17533116.  17533128.  17533140.  17533152.]



```python
dates = num2date(times[:],units=times.units,calendar=times.calendar)
print "dates corresponding to time values:\n",dates
```

    dates corresponding to time values:
    [datetime.datetime(2001, 3, 1, 0, 0) datetime.datetime(2001, 3, 1, 12, 0)
     datetime.datetime(2001, 3, 2, 0, 0) datetime.datetime(2001, 3, 2, 12, 0)
     datetime.datetime(2001, 3, 3, 0, 0)]



```python
for nf in range(10):
    myFile =  Dataset("mftest%s.nc" % nf,"w",format="NETCDF4_CLASSIC")
    myFile.createDimension("q",None)
    x = myFile.createVariable("q","i",("q",))
    x[0:10] = np.arange(nf*10,10*(nf+1))
    while myFile.isopen():
        myFile.close()
```


```python
from netCDF4 import MFDataset
# MFNetCDF4 only works with NETCDF3_* and NETCDF4_CLASSIC formatted files, not NETCDF4
aggFile = MFDataset("mftest*nc")
print aggFile.variables["q"][:]
aggFile.close()
```

    [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32
     33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65
     66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98
     99]



```python
temp3 = rootgrp.createVariable("temp3","f4",("time","level","lat","lon",), zlib=True, least_significant_digit=3)
print temp3
```

    <type 'netCDF4._netCDF4.Variable'>
    float32 temp3(time, level, lat, lon)
        least_significant_digit: 3
    unlimited dimensions: time, level
    current shape = (5, 10, 73, 144)
    filling on, default _FillValue of 9.96920996839e+36 used
    



```python
compoundFile = Dataset("complex.nc", "w")
size = 3 # length of 1-d complex array
# create sample complex data
datac = np.exp(1j*(1.+np.linspace(0, np.pi, size)))
# create complex128 compound data type.
complex128 = np.dtype([("real", np.float64),("imag",np.float64)])
complex128_t = compoundFile.createCompoundType(complex128,"complex128")
# create a variable with this data type, write some data to it.
compoundFile.createDimension("x_dim",None)
v = compoundFile.createVariable("cmplx_var", complex128_t, "x_dim")
data = np.empty(size,complex128) # numpy structured array
data["real"] = datac.real
data["imag"] = data.imag
v[:] = data # write numpy structured array to netcdf compound var
# close and reopen the file, check the contents
compoundFile.close()
compoundFile = Dataset("complex.nc")
v = compoundFile.variables["cmplx_var"]
datain = v[:] # read in all the data into a numpy structured array
# create an emtpy numpy complex array
datac2 = np.empty(datain.shape,np.complex128)
# .. fill it with contexts of structured array
datac2.real = datain["real"]
datac2.imag = datain["imag"]
print datac.dtype,datac
print datac2.dtype,datac2 # data from file
```

    complex128 [ 0.540+0.841j -0.841+0.54j  -0.540-0.841j]
    complex128 [ 0.540+0.j -0.841+0.j -0.540+0.j]



```python
print compoundFile
```

    <type 'netCDF4._netCDF4.Dataset'>
    root group (NETCDF4 data model, file format HDF5):
        dimensions(sizes): x_dim(3)
        variables(dimensions): {'names':[u'real',u'imag'], 'formats':['<f8','<f8'], 'offsets':[0,8], 'itemsize':16, 'aligned':True} [4mcmplx_var[0m(x_dim)
        groups: 
    



```python
print compoundFile.variables["cmplx_var"]
```

    <type 'netCDF4._netCDF4.Variable'>
    compound cmplx_var(x_dim)
    compound data type: {'names':[u'real',u'imag'], 'formats':['<f8','<f8'], 'offsets':[0,8], 'itemsize':16, 'aligned':True}
    unlimited dimensions: x_dim
    current shape = (3,)
    



```python
print compoundFile.cmptypes
```

    OrderedDict([(u'complex128', <type 'netCDF4._netCDF4.CompoundType'>: name = 'complex128', numpy dtype = {'names':[u'real',u'imag'], 'formats':['<f8','<f8'], 'offsets':[0,8], 'itemsize':16, 'aligned':True}
    )])



```python
print compoundFile.cmptypes["complex128"]
compoundFile.close()
```

    <type 'netCDF4._netCDF4.CompoundType'>: name = 'complex128', numpy dtype = {'names':[u'real',u'imag'], 'formats':['<f8','<f8'], 'offsets':[0,8], 'itemsize':16, 'aligned':True}
    



```python
varFile = Dataset("ts_vlen.nc","w")
vlen_t = varFile.createVLType(np.int32, "phony_vlen")
```


```python
x = varFile.createDimension("x",3)
y = varFile.createDimension("y",4)
vlvar = varFile.createVariable("phony_vlen_var", vlen_t,("y","x"))
```


```python
import random
data = np.empty(len(y)*len(x),object)
for n in range(len(y)*len(x)):
    data[n] = np.arange(random.randint(1,10),dtype="int32")+1

data = np.reshape(data,(len(y),len(x)))
vlvar[:] = data
print "vlen variable =\n",vlvar[:]
```

    vlen variable =
    [[array([1, 2], dtype=int32) array([1], dtype=int32) array([1, 2, 3, 4, 5, 6, 7, 8], dtype=int32)]
     [array([1, 2, 3, 4, 5, 6], dtype=int32) array([1, 2, 3], dtype=int32) array([1, 2, 3], dtype=int32)]
     [array([1, 2, 3, 4], dtype=int32) array([1, 2, 3, 4, 5, 6, 7, 8], dtype=int32)
      array([1, 2, 3, 4, 5, 6, 7], dtype=int32)]
     [array([1, 2, 3, 4, 5], dtype=int32) array([1, 2], dtype=int32)
      array([1, 2, 3, 4, 5, 6], dtype=int32)]]



```python
print varFile.variables["phony_vlen_var"]
```

    <type 'netCDF4._netCDF4.Variable'>
    vlen phony_vlen_var(y, x)
    vlen data type: int32
    unlimited dimensions: 
    current shape = (4, 3)
    



```python

```
