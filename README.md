# SkyrimSE-Mods
 Wrapper project for my Skyrim mods. Also my first attempt to understand CMake.
 
 Work in progress.
 
## Usage:
 To clone this repository properly, run this command from the command line:
 
    git clone https://github.com/Vermunds/SkyrimSE-Mods
    git submodule init
    git submodule update

 Make sure vcpkg is set up correctly for [CommonLibSSE](https://github.com/Ryan-rsm-McKenzie/CommonLibSSE).
 
 Run CMake (From the root directory of the project):
 
    cmake -S . -B build
    
 Open the created solution in the build folder.
