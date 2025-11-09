# Skyrim-SE Mods Wrapper

This is a wrapper project for a collection of Skyrim mods created by [Vermunds](https://github.com/Vermunds). These mods are included as submodules along with their common dependency. This wrapper is designed to simplify the management of these mods. 

## Supported Mods

This repository contains the following Skyrim mods:

1. [ClassicSprintingRedone-SE](https://github.com/Vermunds/ClassicSprintingRedone-SE/)
2. [DialogueMovementEnabler-SE](https://github.com/Vermunds/DialogueMovementEnabler-SE)
3. [ExtendedHotkeySystem-SE](https://github.com/Vermunds/ExtendedHotkeySystem-SE)
4. [SkyrimSoulsRE](https://github.com/Vermunds/SkyrimSoulsRE)
5. [TimeFormatChanger-SE](https://github.com/Vermunds/TimeFormatChanger-SE)

Additionally, it includes a common dependency:

- [CommonLibSSE](https://github.com/Vermunds/CommonLibSSE) (fork of [powerof3](https://github.com/powerof3/CommonLibSSE))

**Note:** These mods currently only intended for use with the latest version of Skyrim Anniversary Edition (1.6.1170 for Steam or 1.6.1179 for GOG) and they are not compatible with older versions.

## Build Instructions

### Prerequisites

- CMake
- Visual Studio
- [vcpkg](https://github.com/microsoft/vcpkg) for managing dependencies

### Building

1. Clone this repository and initialize submodules:

   ```shell
   git clone https://github.com/Vermunds/SkyrimSE-Mods.git
   cd SkyrimSE-Mods
   git submodule update --init --recursive
   ```

2. Set up vcpkg: Follow the instructions in the [vcpkg GitHub repository](https://github.com/microsoft/vcpkg) to set up vcpkg on your system if it is not installed already.
   You have to provide the environment variable `VCPKG_ROOT` when configuring this project. It should point to the directory where vcpkg was installed (where `vcpkg.exe` is located).

   
3. Set environment variables for automatic build copy (optional):
   - Option 1: Set `SKYRIM_DATA_PATH` to the Skyrim Data folder (NOT the SKSE/Plugins folder!)
   - Option 2: Set `MO_MODS_FOLDER_PATH` to the Mod Organizer mods folder
   
   Both options cannot be set at the same time.

4. If you do not want to build every included mod, edit `CMakeLists.txt` and remove the `setup_mod(<MOD NAME>)` lines for the mods you don't want to build.

5. Open the folder in Visual Studio **OR** configure and build the project using CMake:

   ```shell
   mkdir build
   cd build
   cmake ..
   cmake --build .
   ```
   
## Contributing

If you'd like to contribute to any of the mods, please refer to the individual mod repositories linked above.

