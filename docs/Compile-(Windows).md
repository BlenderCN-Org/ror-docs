You can download and compile the Rigs of Rods sources completely with tools that are available for free.

Required Software
-----------------

Download and install all required software first:

-   [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- 	[Git](https://git-scm.com)
-   [Python 3](https://www.python.org/downloads/)
-   [CMake](https://cmake.org/download/)
-   Latest RoR dependencies: http://prdownloads.sourceforge.net/rigs-of-rods/CI-build/dependencies.zip (Download the zip, we will unpack it later on). (Note: 64 bit prepackaged dependencies are not available at the moment)
-   Latest RoR sources: https://github.com/RigsOfRods/rigs-of-rods/archive/master.zip (alternatively you can clone them with git from https://github.com/RigsOfRods/rigs-of-rods.git)

(restart your computer after installing all above tools)

Setting up the environment
--------------------------

Since 0.39.2 it is possible to build Rigs of Rods under Windows with precompiled dependencies for 32 bit and 64 bit. Depending on what you compile, some steps might slightly differ. Also, you can only build x64 (64 bit version) on a real 64 bit platform. However, you can still build x86 (32 bit) on a 64 bit platform due to backwards compatibility.

Preparing the source code
-------------------------

-   Create an empty directory anywhere where you have enough free disk space (~3 GB) for example, we chose this now: ``` C:\dev\```
-   unpack the file *master.zip* into that directory. It will create a new directory called ```master```
-   rename unpacked ```master``` to ```rigsofrods-source```
-   unpack dependencies.zip into ```C:\dev\rigsofrods-source``` as directory ```dependencies``` so that it is beside the ```bin``` and ```source``` directories.

<img src="https://archives.rigsofrods.org/wiki/images/4/4c/WinSource3.png" title="WinSource4.png" alt="WinSource4.png" width="500" />

Using CMake
-----------

-   Start cmake from the Windows start menu: START-&gt;Programs-&gt;CMake-&gt;cmake-gui
-   *Where is the source code*: ```C:\dev\rigsofrods-source\```
-   *Where to build the binaries*: ```C:\dev\rigsofrods-source\build_x86```

<img src="https://archives.rigsofrods.org/wiki/images/8/80/WinSource4.png" title="WinSource4.png" alt="WinSource4.png" width="500" />

-   -   Click the ```Configure``` button.
    -   Use ``````Visual Studio 15 2017`````` and ```Use default native compilers``` (first option).
        

<!-- <img src="https://archives.rigsofrods.org/wiki/images/f/f1/SourceArch.png" title="SourceArch.png" alt="SourceArch.png" width="500" /> -->

-   Say ```yes``` if it asked if the directory should be created.
-   Click ```Configure``` twice until all entries are white

<img src="https://archives.rigsofrods.org/wiki/images/7/75/WinSource7.png" title="WinSource7.png" alt="WinSource7.png" width="500" />

-   Click ```Generate``` and close CMake

Compiling the source code
-------------------------

-   Navigate in Windows explorer to the folder ```C:\dev\rigsofrods-source\build_x86``` and open the file RoR.sln. Visual Studio should open. (if you are asked, open with Visual Studio for C++)
<img src="https://archives.rigsofrods.org/wiki/images/7/70/WinSource8.png" title="WinSource8.png" alt="WinSource8.png" width="500" />

-   In Visual Studio, do the following: from the menu: Build -&gt; build Solution (this can take some time, around 15 minutes on an average dualcore/ i7-2600K : 9min)
<img src="https://archives.rigsofrods.org/wiki/images/c/c7/WinSource10.png" title="WinSource10.png" alt="WinSource10.png" width="500" />

-   The compilation is done when you can read something like the following in the bottom text output window:  
  ```========== Build: succeeded ==========```

<img src="https://archives.rigsofrods.org/wiki/images/4/4c/WinSource11.png" title="WinSource11.png" alt="WinSource11.png" width="500" />

Done! After building you will find RoR ready to use in the bin\Release directory. You can navigate with the Windows explorer to ```C:\dev\rigsofrods-source\bin\Release\``` and use it the same way as game directory if it was installed there.
Note: the only difference is that the resources will reside one directory higher, but RoR should figure that out itself.

Troubleshooting
---------------

-   If the compile step is ok but you don't see any executable or the log writes some missing libs, it may be something wrong with the linking step.
-   It may happen that RoR wont start in the bin directory. Simply put the new executable into main release folder and try launch from there. If it doesn't, you simply missed something.
-   If you receive an error such as "fatal error C1083: Cannot open include file: 'stdint.h': No such file or directory " Your dependencies folder may be 'read only'. To fix, right click the dependencies folder, un-tick read only, and apply to all files and sub-directories.
-   If you try to run the compiled RoR Configurator it will tell you that you are missing some .dll files. You then know that you chose the wrong compilation options when compiling wxidgets. We normally link statically against the libs.
-   These compile steps and hints are valid **only** with Visual Studio and Windows, although they may be similar for other configurations.

