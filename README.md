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

**Note:** These mods currently only intended for use with Skyrim Anniversary Edition only (1.6.640 or newer) and they are not compatible with Special Edition. For older versions, refer to older commits in the respective mod repository.

## Build Instructions

### Prerequisites

- CMake (minimum version 3.21)
- Visual Studio (Windows)
- Skyrim Anniversary Edition (1.6.640 or newer)
- [vcpkg](https://github.com/microsoft/vcpkg) for managing dependencies

### Building

1. Clone this repository and initialize submodules:

   ```shell
   git clone https://github.com/Vermunds/SkyrimSE-Mods.git
   cd SkyrimSE-Mods
   git submodule update --init --recursive
   ```

2. Set up vcpkg and install dependencies:
   Follow the instructions in the [vcpkg GitHub repository](https://github.com/microsoft/vcpkg) to set up vcpkg on your system if it is not installed already.
   You have to provide the environment variable `VCPKG_ROOT` when configuring this project. It should point to the directory where vcpkg was installed (where `vcpkg.exe` is located).

4. Open the folder in Visual Studio **OR** configure and build the project using CMake:

   ```shell
   mkdir build
   cd build
   cmake ..
   cmake --build .
   ```

5. The built mod files will be located in the respective mod folders within the `build` directory.

## Contributing

If you'd like to contribute to any of the mods, please refer to the individual mod repositories linked above.
