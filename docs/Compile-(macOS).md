== Build script to automatize building process ==

There is an ongoing effort to create a shell script which fully automatizes the process of building RoR on macOS so even someone with no knowledge of coding could build RoR easily. The script is not finished yet and contributions are greatly appreciated. You can find the script on GitHub: <br> 
https://github.com/RigsOfRods/ror-osx-buildscripts  <br>

Status: Most dependencies are set up correctly.

= Overview =


'''Note:''' These instructions do not make a completely working, finished product and need an experienced Mac user to improve and work on.

Rigs of Rods is a complex beast. It has a 3D engine, physics simulator, water and sky plug-ins, input device system, networking, localization plug-in module... and so on.

Rigs of Rods is built in 4 steps:
* Download source code of RoR and its dependencies
* Compile dependencies
* Generate a Makefile with CMake for RoR
* Use make to build RoR

= Prerequisites =

To compile RoR you will have to work with the Terminal (Applications -> Utilities -> Terminal).

You need the Xcode Command Line Tools

<source lang="bash">
xcode-select --install
</source>

You need the Homebrew package manager to install some dependencies. See http://brew.sh for info. You should install without sudo to minimize risk of damaging your system; Homebrew handles permissions by itself already.

<source lang="bash">
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
</source>

Once Homebrew is installed tap into osrf/simulation, then install these packages:

<source lang="bash">
brew tap osrf/simulation
brew install wget pkgconfig cmake wxwidgets mercurial ogre1.9 gettext (rapidjson – not too sure if it's needed to be honest)
</source>

= Manual patch for minor code edits =

'''Note: I'm working on a new one, this one is currently quite obsolete!''' Here's a patch fixing most compile errors: https://gist.github.com/chilledfrogs/a98f9457f81163f0e90c86f72d1dc60a

Manual linking fixes coming soon...

= Additional dependencies not found in brew repositories =

You have to compile a number of additional dependencies first to be able to compile RoR. Except for OGRE and MyGUI none of these are required to build RoR but highly recommended (or else you will lack vegetation, dynamic sky, multiplayer and scripting), yet I so far only tried to compile with the bare necessities...

From now on the environment variables "ROR_INSTALL_DIR" and "ROR_SOURCE_DIR" have always to be set.
Use the following commands every time you open a new terminal session or save them to your .bashrc so they are saved permanently.

<source lang="bash">
export ROR_SOURCE_DIR="$HOME/rigs-of-rods/source"
export ROR_INSTALL_DIR="$HOME/rigs-of-rods/install"
</source>


=== OGRE dependencies (actually just OIS) ===

<source lang="bash">
cd $ROR_SOURCE_DIR
wget -c -O ogredeps.zip https://bitbucket.org/cabalistic/ogredeps/get/tip.zip
unzip -o ogredeps.zip
cd cabalistic-ogredeps-*
</source>
Remove OSX_DEPLOYMENT_TARGET so we can build with libc++ (requires 10.7+)
<source lang="bash">
sed -i .orig 's/set(CMAKE_OSX_DEPLOYMENT_TARGET 10.6)//g' CMakeLists.txt
cmake -DCMAKE_CXX_FLAGS="-stdlib=libc++" -DCMAKE_INSTALL_PREFIX=$ROR_INSTALL_DIR -DOGREDEPS_BUILD_FREEIMAGE=false -DOGREDEPS_BUILD_FREETYPE=false -DOGREDEPS_BUILD_ZLIB=false -DOGREDEPS_BUILD_ZZIPLIB=false -DOGREDEPS_BUILD_RAPIDJSON=false -DCMAKE_OSX_ARCHITECTURES="x86_64;i686"
make -j2
make install
</source>

== MyGUI ==

<source lang="bash">
cd $ROR_SOURCE_DIR
git clone https://github.com/MyGUI/mygui.git
cd mygui
cmake -DCMAKE_INSTALL_PREFIX=$ROR_INSTALL_DIR -DCMAKE_CXX_FLAGS="-stdlib=libc++" -DCMAKE_BUILD_TYPE:STRING=Release -DMYGUI_BUILD_DEMOS:BOOL=OFF -DMYGUI_BUILD_DOCS:BOOL=OFF -DMYGUI_BUILD_TEST_APP:BOOL=OFF -DMYGUI_BUILD_TOOLS:BOOL=OFF -DMYGUI_BUILD_PLUGINS:BOOL=OFF
make -j2
make install
</source>


== OGRE Paged Geometry ==

For the time being didn't try to build with, so mileage may vary
<source lang="bash">
cd $ROR_SOURCE_DIR
git clone --depth 1 https://github.com/RigsOfRods/ogre-pagedgeometry
cd ogre-pagedgeometry
git pull
cmake -DCMAKE_INSTALL_PREFIX=$ROR_INSTALL_DIR -DCMAKE_CXX_FLAGS="-stdlib=libc++ -std=c++11" -DCMAKE_BUILD_TYPE:STRING=Release -DPAGEDGEOMETRY_BUILD_SAMPLES:BOOL=OFF
make -j2
make install
</source>


== Caelum ==

For the time being didn't try to build with, so mileage may vary
<source lang="bash">
cd $ROR_SOURCE_DIR
git clone https://github.com/RigsOfRods/ogre-caelum
cd ogre-caelum
git pull
cmake -DCMAKE_INSTALL_PREFIX=$ROR_INSTALL_DIR -DCMAKE_CXX_FLAGS="-stdlib=libc++ -std=c++11" -DCaelum_BUILD_SAMPLES:BOOL=OFF
make -j2
make install
</source>
Important step, so the plugin can load:
<source lang="bash">
ln -sf "$ROR_INSTALL_DIR/lib/libCaelum.so" "$ROR_INSTALL_DIR/lib/OGRE/"
</source>


== SocketW ==

For the time being didn't try to build with, so mileage may vary
<source lang="bash">
cd $ROR_SOURCE_DIR
git clone https://github.com/RigsOfRods/socketw
cd mysocketw
git pull
sed -i '/^PREFIX *=/d' Makefile.conf
make CFLAGS="-stdlib=libc++ -std=c++11" CXXFLAGS="-stdlib=libc++ -std=c++11" -j2 dylib
PREFIX="$ROR_INSTALL_DIR" make installosx
</source>


== AngelScript ==

For the time being didn't try to build with, so mileage may vary
<source lang="bash">
cd $ROR_SOURCE_DIR
mkdir angelscript
cd angelscript
wget -c http://www.angelcode.com/angelscript/sdk/files/angelscript_2.22.1.zip
unzip -o angelscript_*.zip
cd sdk/angelscript/projects/gnuc macosx
sed -i '/^LOCAL *=/d' makefile
</source>
make fails when making the symbolic link, this removes the existing versions
<source lang="bash">
rm -f ../../lib/*
make CFLAGS="-stdlib=libc++ -std=c++11" CXXFLAGS="-stdlib=libc++ -std=c++11" -j2
rm -f ../../lib/*
LOCAL="$ROR_INSTALL_DIR" make -s install
</source>


'''IMPORTANT:''' After this step the environment variable "PKG_CONFIG_PATH" has always to be set, otherwise CMake will not be able to find OIS and MyGUI while building RoR. Do this now and every time you open a new Terminal (setting it as $HOME is important, it didn't like the ~ shortcut last time I tried to do it):

<source lang="bash">
export PKG_CONFIG_PATH=$HOME/rigs-of-rods/install/lib/pkgconfig:$PKG_CONFIG_PATH
</source>


= Rigs of Rods =

In $ROR_SOURCE_DIR/source/rigs-of-rods/source/main/CMakeLists.txt comment out (#) the line with USE_MUMBLE, since librt doesn't exist on macOS; I will look for a solution since Mumble *with positional audio* is freely available for macOS... Also keep in mind that the configurator currently can't be built, so you will have to manually edit your config file.

<source lang="bash">
cd $ROR_SOURCE_DIR
git clone https://github.com/RigsOfRods/rigs-of-rods.git
cd rigs-of-rods
git pull
cmake -DCMAKE_INSTALL_PREFIX=~/rigs-of-rods/install/ -DROR_BUILD_CONFIGURATOR=OFF -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_CXX_FLAGS="-pipe -march=native -stdlib=libc++ -std=c++11"
make -j2
cp -R bin "$ROR_INSTALL_DIR/"
</source>
Note: custom link.txt (should be in rigs-of-rods/source/main/CMakeFiles/RoR.dir/) containing (probably) all necessary modifications (mainly libraries to frameworks, and a few missing libraries...) but in quite a messy format, so just compare with the original (you '''will''' get linking errors, trust me) and try to add those that are missing from the original is right here: https://gist.github.com/chilledfrogs/a823a637a4318330b28cbf65a91b8509

Another note: for the time being Settings.cpp (in rigs-of-rods/source/main/utils/) seems to have bad variable names for finding the current working directory, so replace line 216 with:
<source lang="C++">
if (_NSGetExecutablePath(buf, &length) == -1) // Returns absolute path to binary
</source>

If your build was successful you will find the binaries to start the game in ~/rigs-of-rods/install/bin/
'''Note''': so far I noticed what I think are input errors, which might require extra macOS-specific code editing. Please contact me (chilledfrogs) if you know how to fix this.