---
title: Compile (Linux)
---

# Tip: Shell scripts for automatic building


If you just want to build and install RoR without much hassle then have a look at these shell scripts, they bundle all necessary steps:

[Ubuntu/Debian/Mint](https://github.com/RigsOfRods/ror-linux-buildscripts)

[Fedora 26](https://github.com/vbextreme/RigsOfRodsFedora)

# Introduction

Rigs of Rods is very complex and is built on top of many components to provide its features. This results in a long list of dependencies. Most dependencies can usually be found in the software repositories of your distribution but some have to be compiled by hand. Fear not! While this may look overwhelming at first it is actually very easy if you follow these steps closely. <br>

***Note:*** Some dependencies are marked as optional. You can compile and run Rigs of Rods without them. However, we greatly recommend to install them. Chances are you want to use the features they provide sooner or later. Since the developers always compile with all dependencies it is also possible that building without the optional dependencies results in compiler errors. If you want to be on the safe side you should install them, which usually takes only very little time.



# Dependencies of Rigs of Rods
### Library and Software versions required to compile Rigs of Rods

Core requirements:
* C/C++ compiler with support for C++11 (e.g. gcc >= 4.8)
* boost: >= 1.50
* cmake: >= 3.0
* curl
* libssl
* libgtk 2.0
* mygui: >= 3.2.2
* ogre: 1.8.x or 1.9.x
* OIS: 1.3
* openal-soft (any version should work, crashes with 1.15.x)
* wxWidgets >= 2.6

Optional but recommended:
* AngelScript: 2.31.2 (if building without Angelscript you have to modify resources/particles/water.particle and remove "affector FireExtinguisher {effectiveness 	1}" )
* Caelum: version 0.6.1 (for Ogre 1.8) or >= 0.6.2 (Ogre 1.9) - sky plugin, provides dynamic sky with day/night, weather and clouds
* socketw: >= 1.0.0
* nvidia-cg-toolkit - required for Cg shader effects which some mods use. Works with every GPU regardless of vendor. Not libre software. Requires Ogre to be compiled by hand.
* paged geometry: >=1.1.0 (Ogre 1.8), >= 1.2.0 (Ogre 1.9)


# Installing required packages usually found in repositories

### Ubuntu, Debian, Linux Mint and derivatives


``` bash
sudo apt-get update
sudo apt-get -q install build-essential git cmake pkg-config libboost-all-dev mercurial \
libfreetype6-dev libfreeimage-dev libzzip-dev libois-dev \
libgl1-mesa-dev libglu1-mesa-dev libopenal-dev  \
libx11-dev libxt-dev libxaw7-dev libxrandr-dev \
libssl-dev libcurl4-openssl-dev libgtk2.0-dev libwxgtk3.0-dev \
nvidia-cg-toolkit
```
### Fedora and derivatives


**dnf** became the default tool for installing software in Fedora 23. Try substituting **yum** in earlier releases. If you don*t want to be prompted to confirm package installation, then add the ***-y*** flag immediately after **dnf**
``` bash
# Roughly the equivalent of Ubuntu*s build-essential
sudo dnf install make automake gcc gcc-c++ kernel-devel doxygen RedHat-rpm-config
# The equivalent of libboost-all-dev
sudo dnf install boost-devel boost-mpich-devel boost-openmpi-devel boost-python3-devel libatomic-static
# Everything else
sudo dnf install git cmake pkgconfig mercurial \
freetype-devel freeimage-devel zziplib-devel ois-devel \
mesa-libGL-devel mesa-libGLU-devel openal-soft-devel  \
libX11-devel libXt-devel libXaw-devel libXrandr-devel \
openssl-devel libcurl-devel gtk2-devel wxGTK3-devel
# You can use this if dnf info ogre-devel shows at least version 1.9.0
sudo dnf install ogre-devel
# New enough Fedora may have MyGui
sudo dnf install mygui-devel
# New enough Fedora may also have the pagedgeometry plugin, saving you building it later
sudo dnf install ogre-pagedgeometry-devel
```

The Nvidia CG toolkit is obsolete and now no longer in the Fedora repositories.  However, you can download an RPM for RedHat Linux, which should also be OK with Fedora from the [Nvidia CG Toolkit webpage](https://developer.nvidia.com/cg-toolkit-download).

# Compiling and installing missing packages
Notes:
* add -j***X*** after make for multithreaded building. More threads require more RAM. Example for quad-core: make -j4
* if you want to avoid the use of sudo you can use CMAKE_INSTALL_PREFIX. This is not covered in these instructions. For an example see the Debian build scripts linked at the top of this page


It is recommended to create a directory for the dependencies:
``` bash
mkdir ~/ror-deps
cd ~/ror-deps
```


## Ogre and its dependencies

Ogre version 1.9.x is required to build RoR. It*s provided by many software repositories. Compiling by hand is only necessary if:
* you want to see Cg shader effects (needs nvidia-cg-toolkit to be installed) - otherwise black/yellow placeholders will be shown
* your distribution doesn*t have Ogre 1.9 in its repositories
* note: The experimental GL3+ rendering subsystem doesn*t work with RoR and thus is not a reason to build from source

### Ogredeps



Ogre has some dependencies on its own which can be downloaded and built in one package. The package contains:
freeimage, freetype, OIS, zlib and zziplib

If you use Debian/Ubuntu/Linux Mint and ran the apt-get install above you can skip the installation of ogredeps.

If you use a sufficiently new Fedora and the **dnf install** command for ogre-devel worked, then you can skip the installation of ogredeps.

``` bash
wget https://bitbucket.org/cabalistic/ogredeps/get/tip.zip -O ogredeps.zip
unzip ogredeps.zip
cd cabalistic-ogredeps-*
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local
make
sudo make install
cd ..
```


To build RoR, you will later need to create a file with such content into /usr/share/pkgconfig/OIS.pc if none has been created:

```
prefix=/usr/local
exec_prefix=${prefix}
libdir=${prefix}/lib
includedir=${prefix}/include
Name: OIS
Description: OIS
Version: 1.4.0
URL:
Libs: -L${libdir} -lOIS
Cflags: -I${includedir} -I${includedir}/OIS
```

### Ogre itself (rendering engine)

Ogre 3D is the rendering engine of Rigs of Rods.

``` bash
hg clone https://bitbucket.org/sinbad/ogre -b v1-9
cd ogre
cmake \
-DFREETYPE_INCLUDE_DIR=/usr/include/freetype2/ \
-DCMAKE_BUILD_TYPE:STRING=RelWithDebInfo \
-DOGRE_BUILD_SAMPLES:BOOL=OFF \
.
make
sudo make install
cd ..
```
After building, the generated libOgreMain.so.1.9.0 should be about 100Mb.

## MyGUI (GUI)

As the name implies MyGUI is needed for the GUI. This is available in recent Fedora repositories. If the **dnf install** described above has succeeded, then you will not need to build from source.

``` bash
git clone https://github.com/MyGUI/mygui
cd mygui
git pull
cmake \
-DFREETYPE_INCLUDE_DIR=/usr/include/freetype2/ \
-DCMAKE_BUILD_TYPE:STRING=RelWithDebInfo \
-DMYGUI_BUILD_DEMOS:BOOL=OFF \
-DMYGUI_BUILD_DOCS:BOOL=OFF \
-DMYGUI_BUILD_TEST_APP:BOOL=OFF \
-DMYGUI_BUILD_TOOLS:BOOL=OFF \
-DMYGUI_BUILD_PLUGINS:BOOL=OFF \
.
make
sudo make install
cd ..
```

## Paged Geometry (vegetation - optional)

Paged Geometry is a plugin for OGRE taking care of vegetation. Without Paged Geometry, there will be no vegetation at all.

Modern versions of Fedora include this plugin in their repositories. If the **dnf install** command above succeeded, then you may not need these instructions to build from source.

``` bash
git clone https://github.com/RigsOfRods/ogre-pagedgeometry
cd ogre-pagedgeometry
cmake \
-DCMAKE_BUILD_TYPE:STRING=RelWithDebInfo \
-DPAGEDGEOMETRY_BUILD_SAMPLES:BOOL=OFF \
.
make
sudo make install
cd ..
```

Since there is no uninstall script run this to remove PagedGeometry
``` bash
sudo rm /usr/local/lib/libPagedGeometry.so
sudo rm -rf /usr/local/include/Paged
```

## Caelum (dynamic sky - optional)

Caelum is a sky plugin for OGRE. Without a sky plugin, you will only have a skybox, a static image filling the sky. Sky plugins make the sky look more realistic. They allow setting the time of day which is impossible without a sky plugin. Clouds and weather are only available with a sky plugin.
Caelum only works with OpenGL and Direct3D but not with OpenGL ES.

``` bash
git clone https://github.com/RigsOfRods/ogre-caelum
cd caelum
git pull
cmake .
make
sudo make install
# important step, so the plugin can load:
sudo ln -s /usr/local/lib/libCaelum.so /usr/local/lib/OGRE/
cd ..
```

The penultimate line above will need changing if you were able to install OGRE from repositories. For example if installed using **dnf install** on Fedora as described above, the OGRE directory will be in either /usr/lib (on 32-bit Fedora) or /usr/lib64 (on 64-bit Fedora). In which case, you should instead use:
``` bash
sudo ln -s /usr/local/lib/libCaelum.so /usr/lib/OGRE/
```
or
``` bash
sudo ln -s /usr/local/lib/libCaelum.so /usr/lib64/OGRE/
```

Since there is no uninstall script run this to remove Caelum
``` bash
sudo rm /usr/local/lib/libCaelum.a
sudo rm -rf /usr/local/include/Caelum
```

## SocketW (network play - optional)

MySocketW provides OS socket abstraction and is required for network play/multiplayer.

NOTE: You have to install the OpenSSL development package before you compile (libssl is not required if you are using Debian/Ubuntu and derivatives and ran the apt-get install command above)
NOTE: There is a SocketW package in usual repositories but RoR seems to need the one from git.

``` bash
git clone https://github.com/RigsOfRods/socketw.git
cd mysocketw
make -j2 shared
sudo make install
cd ..
```

Since there is no uninstall script run this to uninstall SocketW.

``` bash
sudo rm /usr/local/lib/libSocketW*
sudo rm /usr/local/include/SocketW.h
sudo rm /usr/local/include/sw_*.h
```

## AngelScript  (scripting - optional)

AngelScript is required for scripting support. Scripting allows the use of the benchmark map, AI, races and server mods among others.

``` bash
mkdir angelscript
cd angelscript
wget http://www.angelcode.com/angelscript/sdk/files/angelscript_2.31.2.zip
unzip angelscript_*.zip
cd sdk/angelscript/projects/gnuc
SHARED=1 VERSION=2.31.2 make

# sudo make install fails when making the symbolic link, this removes the existing versions
rm -f ../../lib/*
sudo SHARED=1 VERSION=2.31.2 make install

#cleanup files made by root
rm -f ../../lib/*
cd ../../../../../
```


# Building Rigs of Rods

Download the source code:
``` bash
cd ~/
git clone https://github.com/RigsOfRods/rigs-of-rods.git
cd rigs-of-rods
```


# Configuring your build

Before you can compile the code you have to configure your build by running cmake. cmake automatically detects what platform you are using, finds the dependencies, sets up the flags for the compiler accordingly etc..

There are two ways to run cmake, one is to run cmake from the console. This is straight forward and thus recommended. Instead, you can use cmake-gui if you prefer GUIs. The GUI has the advantage that it shows multiple configuration options which are not shown by the CLI. But you usually don*t need them anyway.

Note: if you built some dependencies from sources, then check that the install commands copied the packageconfig files (.pc) from /usr/local/lib/pkgconfig to /usr/share/pkgconfig. If not, you will probably experience some *package XXX not found* simply because cmake/pckconfig cannot find the XXX.pc files.

## CLI (recommended)

``` bash
cmake .
```

## GUI  

The cmake GUI may not be provided on all systems or has to be installed in addition to cmake.<br> To start the GUI for cmake run:
``` bash
cmake-gui .
```

* click "Configure"
* select "Unix Makefiles" and "Use default native compilers" (that are the default options) and press "Finish"
* click "Configure" again until all list entries are white
* look at the log window for information and errors (look for red lines with " &gt; " in front)
* enable the components you want to compile in: ROR_*
* click "Configure" again until the "Generate" button is click-able
* click "Generate"
* close cmake-gui

# Building the source code

To start building simply run:
``` bash
make
```

Rigs of rods parallel make seems to work reliably, so if you have a machine with lots of cores, you can build faster. For example of a 6 core hyperthreaded machine you could try
``` bash
make -j 12
```

If the build completed without any errors, you have to build the zip_and_copy_resources target in order to copy the resources required for running RoR.
``` bash
make zip_and_copy_resources
```

That*s it! You finally built Rigs of Rods. If you never ran Rigs of Rods before you need to run RoRConfig at least once before RoR. RoRConfig sets up the directory for configuration and other files.

# Updating existing sources  

If you already got the sources and just want to update, follow the steps below:
``` bash
cd rigs-of-rods
git pull

rm -f CMakeCache.txt
find . -name CmakeFiles -exec rm -rf {} \;
cmake .

make
```
The use of **find** will give warnings about the directories no longer existing&mdash;that*s a side effect of using **find** to remove directories and can be ignored.
