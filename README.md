Original readme.txt:


                     PANARD VISION REALTIME 3D ENGINE
                          	SDK 0.99b-Beta6
                       (C) 1997-2002, Olivier Brunet

	                      2002-04-23


GPL:
----

This is the first release of PV under GPL (it was commercial before). 
This should compile ok with Visual C++ 6.0 (SP3 or higher
required for STL things). You will find a VC workspace in src/ directory.
This is complete source code of the latest PV Engine and all drivers.

This code is released under GPL license (see COPYING file). If you have commercial questions
see the Contact part at the end of this document.

How to setup the compile environement:
- You will need Borland's Tasm to compile the .asm (or an assembler compatible with it), or you may 
use the C version of the asm routines (much slower, but compiles anywhere), see config.h.
- Setup your tasm directory in the Tools->Options->Executable dir of MSVC

If you have any questions regarding compilation, problems, or whatever about the GPL version of PV
please direct them to the pvision-users mailing list (see at the end of this document), not directly
to me.

If you have patch, bug fixes, enhancement send them in I will integrate them and setup a CVS if needed.


*************************** Things that you should take care of:


Linux:
------
Some things are not implemented in this Beta Release :
 - The 3dfx driver (with source) compile but has not been tested at all
 - Indentation of sources are "funky"
 - The Opengl Hardware support seems ok but fails on some version of NVidia
 drivers due to the dlopen() bug, see the NVidia FAQ about this.
Reports success or errors to pvision@panardvision.com please.

Panard Scape:
------------
  The new landscape library (based on ROAM algorithm). See samples\pscape\tut1 for a sample program.
This program loads 8*8 blocks, each of these blocks are 128*128 (yes it's a 1024*1024 landscape). Each
of these blocks have a texture (in this case it's always the same => ugly, but I don't have time
to play further with Bryce and Paintshop pro). Blocks are loaded asynchronously as they approach viewer
and unloaded as they are not used anymore. This allow for really big landscape since not everything 
is in memory. The program is not really fine tuned, so maybe they can be on screen and not be finished
loading (if you move too fast), then you will see a hole, and then the part will appear and then refine.

For now you still need I think quite a lot of memeory to start the program or you will have to wait 
a looooooong time.
Also, the clipping planes are very distant => on old hardware (voodoo 1) this can cause ZBuffer
artefacts.

If you want you can add lighting and detail texturing with simple #defines (see sources).

You can also use a smaller landscape, to do this, in pscape\bin do this:

splitter ..\data\tut1.hf.small 8 ..\data\tut1

This little utility will split tut1.hf into 8 blocks and store them as tut1_x-y.r16

The engine reads Bryce's .hf files, and terragen's raw export 16 bits files (they must be named .r16 to
be recognized).

If you want to contribute a .dem reader you're welcome (see the pscape directory to see how is 
built a terrain driver) :)

3Dfx driver:
-----------
Source of the hardware driver is included with this release (pv3dfx.dll).

PLEASE:
------
Report anything that sounds strange or not working to pvision@panardvision.com.
If you have questions, or need help with the new features send mails to the pvision-users mailing list
(see mailing.txt)

				
                             RELEASE NOTES
                             =============

        (see history.txt to see changes since last official release)
               (see LICENSE file before using these libs)

TOC
---
	0/ Compilers supported	
	0.5/ If you upgrade from 0.98 and 0.98a SDK: READ THIS
	0.6/ Users of all previous version of PV: READ THIS
	I/ Directories
	II/ Installation
		 a/ Win32 Users
		 b/ Linux Users
		 c/ Dos Users (WATCOM)
		 d/ Dos Users (DJGPP)
		 e/ All Users		<-- Read it !
	III/ Notes		
		a/ Direct3D5 Driver (pvd3d.dll) (requires DirectX5)
		b/ Direct3D6 Driver (pvd3d6.dll) (requires DirectX6)
		c/ Direct3D7 Driver (pvd3d7.dll) (requires DirectX7)
		d/ Direct3D8 Driver (pvd3d8.dll) (requires DirectX8)
		e/ OpenGL Driver (pvgl.dll)		
		f/ 3Dfx Driver (pv3dfx.dll) (requires Glide 2.4x)
		g/ Quake I&II Driver		
		h/ Samples
		i/ Borland users
		j/ Special notes for TNT owners
	IV/ Known bugs/issues
	V/  Help me
	VI/ Contact & help with PV
	

0/ Compilers supported
----------------------
        - Visual C/C++ 5.0 and 6.0 for win32 
	- Borland compilers
	- Should work with all win32 compilers (tested with CodeWarrior)
	  If you manage to use PV with other compilers, tell me.
        - Watcom C/C++ 10+ for dos
	- DJGPP for dos
        - GNU gcc for Unices.
	
0.5/ If you upgrade from 0.98 and 0.98a SDK: READ THIS
------------------------------------------------------
  Some type names changed in this release, see history.txt. To be able to compile your old
code with the new header files, include the compat.h file at the begining of your
sources. It will map old names, to new names. Your are highly advised to use the new names
for now.
  Additionnaly all struct members and functions that were using Vertexes in their name
can now be accessed using the more correct Vertices in place of Vertexes. The old
names are not available anymore.

VERY IMPORTANT: You should recompile ALL your apps working with PV. The 0.98b libs are not 
binary compatible with 0.98(a).

IMPORTANT TOO: You must now use PV_MeshSetupPos() to change the position of
a mesh instead of directly accessing his Pos member. Same with
PV_MeshSetMatrixDirect and the Matrix member.

LAST BUT NOT LEAST: If you use PV with something else than VC under windows
read the memory management paragraph in the doc.

0.6/ Users of all previous version of PV: READ THIS
---------------------------------------------------
  0.99 version changed how faces are handled. You can't directly fill Face.V[x] directly
anymore. You have to allocate space for vertices index now. Before you did something like 
that:
	mesh1=PV_SimpleCreateMesh(something)
	Mesh1->Face[0].NbrVertexes=4;
	Mesh1->Face[0].V[0]=0;
	Mesh1->Face[0].V[1]=1;
	Mesh1->Face[0].V[2]=2;
	Mesh1->Face[0].V[3]=3;

Now this code would be:
	unsigned V;

	mesh1=PV_SimpleCreateMesh(something)
	Mesh1->Face[0].NbrVertexes=4;
	V[0]=0;
	V[1]=1;
	V[2]=2;
	V[3]=3;
	PV_SetVerticesToFace(&Mesh1->Face[0],V,4);

Don't cry, it saves a LOT of memory at runtime :) Read the documentation of 
PV_SetVerticesToFace() and see changes in tut14 for details.


I/ Directories
--------------
        DOC\
                Contains documention for this release in html, and pdf format.
        SRC\
                Contains released sources of the engine.
        QKREAD\
                Contains sources for the Quake I level driver.
        INCLUDE\
                Contains include files to use PV.
        LIB\
                Contains the linkable libraries (.lib or .a (for linux)) and
                the dynamic libraries  (for Win32 and Linux , as .dll and .so).
	LIB\BORLAND\
		Contains import libraries for Borland compilers.
		(win32 only)
        DXKIT\
                Simple and efficient DirectDraw wrapper used by samples.
                (win32 only)
        PVUT\
                Sources of the Panard Vision Utilities Library.
        VIEWER\
                Binary and sample files of the Panard Viewer.
        VIEWERSRC\
                Sources of the Panard Viewer.
        SAMPLES\
                Some samples to demonstrates PV use. See SAMPLES\README.TXT
                for more infos.
	SAMPLES\README.TXT
		READ IT !!!! List of tutorials and what they do.
	BIN\	
		Some more or less useful utilities
	3DS2DEF\
		.def file skeleton generator sources. Generates a .def file
		from material definitions contained in a .3ds
	PCONF\
		Some emitters&operators for Panard Confettis (the particle system)
		source. If you want to develop new operators and/or emitters
		types, this is your resource. (sample)
	PV3DFX\
		Sources of the 3Dfx Glide hardware driver (pv3dfx.dll). For
		educational purpose on how to write hardware drivers.
	PSCAPE\
		Sources of the terrains drivers for Panard Scape. (sample)
	PVCORE\
		Sources of a JPG image loader for the unified image loader system (sample)
	FLEXPORTER\
		3DSMAX 3 Export plugin (Flexporter 1.06), see the readme.txt file for instructions.
		The Panard Vision plugin for Flexporter is pvbexport.dli and should be placed into
		your MAX dir with other flexporter's dli files. pvcore.dll MUST be accessible by 
		pvbexport.dli (in your windows\system dir for instance).

II/ INSTALLATION
----------------

 a/ Win32 Users
 --------------

   You will need the DirectX 7 SDK installed in order to recompile the samples.
   The minimal DX runtime required to run them is DX5 (or DX3 under NT4).

   All the makefiles and project files have been generated with VC6.

   Some samples require Visual Studio 5 Service Pack 3 to recompile.

NOTE: The setup program installs the Visual C runtime and the MFC runtime (MSVCIRT.DLL,
      MSVCP60.DLL,MSVCRT.DLL and MFC42.DLL) in your WINDOWS\SYSTEM directory, but only 
      if those included with PV are more recent.
      The setup program installs all the PV runtime in WINDOWS\SYSTEM. This is only for
      developement purposes, when shipping an application using PV, put the PV runtime
      in the same directory than your exe.


 b/ Linux Users
 --------------
  LINUX: This is an elf-i386-glibc2 release.
   
  If you want to use the dynamic libraries copy the *.so files from LIB\
to a directory containing the dynamic libraries on your system
(/usr/local/lib by example).
Run ldconfig as root to record the newly installed libs.

  Or setup the LD_LIBRARY_PATH environment variable to point where the
libs are:
   (with bash) export LD_LIBRARY_PATH='/home/Login/pvision/lib'
   Other shells will have something like setenv instead of export.

  You will need the X-Window windowing system in order to run the samples.

  DON'T FORGET TO EDIT THE Make-config FILE IN THE BASE DIRECTORY TO MATCH
YOUR SYSTEM. This is required if you want to recompile the binaries 
(pre-compiled binaries are in viewer/ and samples/bin/)

   Makefiles are GNU Makefiles.

   You may need to be root to use the 3DFX libs. And remember that the
rendering window MUST have focus under X, otherwise you will not be able
to control the samples and be forced to to a blind reboot. So be quick to
focus the render window when she pop up, or connect a second screen to your
3dfx :)

 c/ Dos Users (WATCOM)
 ---------------------
   Makefiles are WMAKE makefiles. You will need the Scitech SVGA Kit 6.x from
Scitech Software (it's free) in order to recompile the Panard Viewer and
samples. See the Panard Vision home page.

 d/ Dos Users (DJGPP)
 --------------------
   The great DJGPP port of the viewer and the samples framework has been done
by Maik Haehnel <5k@writeme.com>. Thanks a lot !!

   The archive contains long file name, be sure to compile with the +lfn option
set to yes (in the djgpp.env file) under win95.

   Makefiles are GNU-MAKE makefiles. You will need the PTC library (0.72) to
recompile the viewer and samples, see http://www.gaffer.org/. See Panard Vision
Home page for a precompiled version.

  DON'T FORGET TO EDIT THE MAKECF FILE IN THE BASE DIRECTORY TO MATCH
YOUR SYSTEM. This is required if you want to recompile the binaries 
(pre-compiled binaries are in viewer/ and samples/bin/)

   This version is compiled with pgcc 1.0., edit MAKECF to set parameters for 
other compilers.

   The full recompilation is launched by typing 'build' in the root of the 
PVSDK distribution.


 e/ All Users
 ------------
   Set up your compiler to look for include files in <PVSDK>\INCLUDE\ and library in
<PVSDK>\LIB\ (or <PVSDK>\LIB\BORLAND\ for borland users).
   If you are under Win32, add the <PVSDK>\DXKIT\ directory in the path searched by your
compiler for include files AND library files.

Look the samples in VIEWERSRC\ and SAMPLES\ to see how the beast works.

   You will need glide 2.4x properly installed to use the native
3dfx driver.


III/ NOTES
-----------
  Portals does not support space wrapping at this time (ie the transformations matrices are
NOT modified when going 'into' the portal), but this may come.

WIN32 Users: You must link your applications with the C runtime as a DLL, see
             the doc for more explanations.

a/ Direct3D5 Driver (pvd3d.dll) (requires DirectX5)
---------------------------------------------------
  The Direct3D driver generates a pvd3d.log file each time it is run. 
If you got any troubles with it *please* contact me describing the more precisely
possible your problem, your hardware config and don't forget to send me the corresponding 
pvd3d.log file.

  You can disable the generation of the pvd3d.log, by setting the environnement variable
PVD3D to QUIET (case sensitive).
eg: set PVD3D=QUIET

NOTE: Starting with driver shipped with 0.99a, this driver try to initialize the DirectX6
      driver, if it succeeds, then DX6 driver is used in place of the DX5 driver, otherwise
      it falls back to default dx5 mode. It's totally transparent to user, and permits
      dynamic selection of the best driver available.

b/ Direct3D6 Driver (pvd3d6.dll) (requires DirectX6)
----------------------------------------------------
  The Direct3D6 driver generates a pvd3d6.log file each time it is run. 
If you got any troubles with it *please* contact me describing the more precisely
possible your problem, your hardware config and don't forget to send me the corresponding 
pvd3d6.log file.

  This driver adds (over DirectX5 driver) support for multitexturing on hardware 
that supports it, as well as texture swapping.

  You can disable the generation of the pvd3d6.log, by setting the environnement variable
PVD3D6 to QUIET (case sensitive).
eg: set PVD3D6=QUIET

c/ Direct3D7 Driver (pvd3d7.dll) (requires DirectX7)
----------------------------------------------------
  The Direct3D7 driver generates a pvd3d7.log file each time it is run. 
If you got any troubles with it *please* contact me describing the more precisely
possible your problem, your hardware config and don't forget to send me the corresponding 
pvd3d7.log file.

  This driver adds (over DirectX6 driver) support for hardware transform&lighting.

  You can disable the generation of the pvd3d7.log, by setting the environnement variable
PVD3D7 to QUIET (case sensitive).
eg: set PVD3D7=QUIET

d/ Direct3D8 Driver (pvd3d8.dll) (requires DirectX8)
----------------------------------------------------
  The Direct3D8 driver generates a pvd3d8.log file each time it is run. 
If you got any troubles with it *please* contact me describing the more precisely
possible your problem, your hardware config and don't forget to send me the corresponding 
pvd3d8.log file.

  This driver adds (over DirectX7 driver) better support for hardware transform&lighting.

  You can disable the generation of the pvd3d8.log, by setting the environnement variable
PVD3D8 to QUIET (case sensitive).
eg: set PVD3D8=QUIET


e/ OpenGL Driver (pvgl.dll)
---------------------------
  The OpenGL Driver needs a fully compliant GL implementation, It works with
the GL shipped with 95 OSR2, as well as SGI's CosmoGL. TNT GL works great too,
but some problems are still showing up due to some bugs in the TNT GL 
implementation (tested with Detonator 1.09).

f/ 3Dfx Driver (pv3dfx.dll) (requires Glide 2.4x)
-------------------------------------------------
  0.99 ships with a totally new driver supporting MultiTexturing (on V2) and
texture swapping. It runs with glide 2.4x (glide2x.dll).
  Please report any problems.

RUSH USERS: There is a support for windowed rendering in pv3dfx.dll. Just set the PV3DFX 
	    environnement variable to WINDOWED (case sensitive), either in your shell
	    or in your program (before InitHardware()) like this:
		putenv("PV3DFX=WINDOWED");
	    This feature has not been tested, please report any problems you find.

g/ Quake I&II Driver
--------------------
    There is no level packed with the SDK, so to test it you'll have to extract a .bsp
from a .pak file or create a .bsp with a quake editor.
    The win32 and dos version of PV have an utility named XPAK which is able to extract
files from .pak archives.
    When viewing a Quake level, don't walk 'outside' the level (use collision detection 
feature if you want), or the Quake's PVS will become useless and the frame rate will drop.

h/ Samples
----------
   The samples shortcuts for the Panard Viewer are targeted for a resolution of 
320*200*(8 or16 bits) or 640*480. If your video card doesn't support these modes, 
the samples will not work. To correct this, edit the shortcuts/batchfile and use a 
resolution supported by your hardware (-x and -y parameters).

i/ Borland users
----------------
  There is no Borland project file shipped with this SDK (.MAK files are Visual C makefiles
and are not usable with Borland's compilers). To build a project, include the your .c and .cpp
files as well as pvision.lib, pvut.lib, pconf.lib and so on depending of the part of the engine
you use (pvision.lib is required in any case). 
  You will also need DDraw.lib, DirectDrawEasy.Cpp if you use the DirectDraw framework provided
with the sdk. All tuts (in the samples\ subdirectory) use it.

  For instance to recompile tut2 you will need in your project:
     main.cpp
     DirectDrawEasy.cpp
     pvision.lib
     pvd3d.lib 
     ddraw.lib
     pvcbuild.def (see below)

  You can also choose to include in your project all the libs in the borland directory of the
SDK and let the linker choose which libs are needed.

  If you have trouble with the provided libs in the lib\Borland directory, you can
regenerate them from the PV DLLs using the IMPLIB utility located in your Borland 
product bin\ directory.

The syntax is: IMPLIB PVISION.LIB PVISION.DLL
and so on for other DLLs in lib\

If you still have problems like linking errors add the file lib\borland\pvcbuild.def 
to your project file.
If you want to add hardware support and got static linking errors with hardware driver 
on the _PVDriver symbol, try to add this in your pvcbuild.def:

	_PVDriver=pvd3d.1

replacing pvd3d by the name of the dll driver you want (like pvgl or pv3dfx).
For infos, 1 is the ordinal number of PVDriver in all dlls drivers.


j/ Special notes for TNT owners
-------------------------------
New starting with 0.99a is the stencil based shadow function of PV. To use this
you need to have a stencil enabled hardware with a stencil enabled driver 
(pvd3d6.dll or pvgl.dll).
The stencil buffer on TNT based board is only actived when using 32bits 
rendering. In order to have shadow effects or use the stencil functions of PV
be sure to run your applications in full screen 32bits, or windowed with a 32
bits desktop.
You can check wether the stencil buffer is enabled by looking into the pvd3d6.log
file, or the startup on-console info for pvgl.dll.

IV/ Known bugs/issues
---------------------
Some problems occur with resampled textures in quake levels.

V/ Help me
----------

I'm not an artist. So, I'm looking for some cool 3D scene to demonstrates some nice 
features of PV. So if you have some nice 3ds near you, contact me.

I'm also looking for a cool editor to fully support, in order to have an
homogenous framework as a base for future advanced features.
The current software which seems interesting are 3dsmax2 (via a dedicated
plugin) and maybe MultiGen's editors (via OpenFlight).
If you know good editors (if they are free and portable that's over cool)
tell me.

VI/ Contact & help with PV
--------------------------

You can reach me at: pvision@panardvision.com

If you have comments, ideas, found bugs, don't hesistate to mail me.

Please direct any questions reguarding PV usage to the Panard-Vision-Users mailing list
see the mailing.txt file.

Panard Vision Home Page: http://www.panardvision.com/
(license informations available)

