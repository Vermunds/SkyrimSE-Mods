# Skyrim-SE Mods Wrapper

This is a wrapper project for a collection of Skyrim mods created by [Vermunds](https://github.com/Vermunds). These mods are included as submodules along with their common dependencies. This wrapper is designed to simplify the management of these mods. 

## Supported Mods

This repository contains the following Skyrim mods:

1. [ClassicSprintingRedone-SE](https://github.com/Vermunds/ClassicSprintingRedone-SE/)
2. [Skyrim-CleanSaveAutoReloader](https://github.com/Vermunds/Skyrim-CleanSaveAutoReloader)
3. [DialogueMovementEnabler-SE](https://github.com/Vermunds/DialogueMovementEnabler-SE)
4. [ExtendedHotkeySystem-SE](https://github.com/Vermunds/ExtendedHotkeySystem-SE)
5. [SkyrimSoulsRE](https://github.com/Vermunds/SkyrimSoulsRE)
6. [TimeFormatChanger-SE](https://github.com/Vermunds/TimeFormatChanger-SE)

Additionally, it includes these common dependencies:

- [CommonLibSSE](https://github.com/Vermunds/CommonLibSSE) (fork of [powerof3](https://github.com/powerof3/CommonLibSSE))
- [ModConfigUI](https://github.com/Vermunds/ModConfigUI) - the in-game settings menu shared by the mods
- [SKSE Menu Framework API](https://github.com/QTR-Modding/SKSE-Menu-Framework-3-API) and [Fuzz's Legally Intelligible Core Kit](https://github.com/Fuzzlesz/FUCK) - the menu frameworks ModConfigUI draws its menu with

**Note:** These mods are currently only intended for use with the latest version of Skyrim Anniversary Edition (1.6.1170 for Steam or 1.6.1179 for GOG) and they are not compatible with older versions.

## Build Instructions

### Prerequisites

- CMake 3.31 or newer
- Visual Studio 2022 with the "Desktop development with C++" workload (provides the MSVC compiler and Ninja)
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
   The dependencies are installed automatically when the project is configured.

3. Set up automatic deployment after each build (optional). Set one of these CMake cache variables:
   - `SKYRIM_DATA_PATH` - the Skyrim Data folder (NOT the SKSE/Plugins folder!)
   - `MO_MODS_FOLDER_PATH` - the Mod Organizer mods folder, each mod is copied into its own folder there

   Both options cannot be set at the same time. Only the built DLL and PDB files are copied, not the translation or .ini files.
   The easiest way is a `CMakeUserPresets.json` next to `CMakePresets.json` (it is ignored by git):

   ```json
   {
     "version": 3,
     "configurePresets": [
       {
         "name": "Release-Deploy",
         "inherits": "Release",
         "cacheVariables": {
           "MO_MODS_FOLDER_PATH": "C:/Mod Organizer/mods"
         }
       }
     ]
   }
   ```

4. If you do not want to build every included mod, edit `CMakeLists.txt` and remove the `setup_mod(<MOD NAME>)` lines for the mods you don't want to build.

5. Open the folder in Visual Studio and select a preset, **OR** configure and build it from the "x64 Native Tools Command Prompt for VS 2022":

   ```shell
   cmake --preset Release
   cmake --build build/Release
   ```

   The `Debug` and `Release` presets are available, each builds into `build/<preset name>`.

## Contributing

If you'd like to contribute to any of the mods, please refer to the individual mod repositories linked above.

## License

This software is available under the GNU General Public License v3.0 or later, with a modding exception. See [LICENSE](./LICENSE) and [EXCEPTIONS.md](./EXCEPTIONS.md) for details.
