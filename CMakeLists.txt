cmake_minimum_required(VERSION 3.21)
set(CMAKE_SYSTEM_VERSION 10.0)

# Set up vcpkg
if(DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif()
set(VCPKG_TARGET_TRIPLET "x64-windows-static-md" CACHE STRING "")

project(
  SkyrimRE
  LANGUAGES CXX
)

# Include guards
if("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
	message(FATAL_ERROR "in-source builds are not allowed")
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Install paths

# SKYRIM_DATA_PATH - Path to Skyrim Data folder (NOT the SKSE/Plugins folder!)
# MO_MODS_FOLDER_PATH - Path to Mod Organizer mods folder
# Both can't be set at the same time.

if(SKYRIM_DATA_PATH AND MO_MODS_FOLDER_PATH)
    message(FATAL_ERROR "Both SKYRIM_DATA_PATH and MO_MODS_FOLDER_PATH cannot be set simultaneously.")
endif()

if(NOT SKYRIM_DATA_PATH AND NOT MO_MODS_FOLDER_PATH)
    message(WARNING "Neither SKYRIM_DATA_PATH nor MO_MODS_FOLDER_PATH is set. Automatic post-build copies will be disabled.")
endif()

# Set up CommonLibSSE
message(STATUS "Setting up CommonLibSSE...")
add_compile_definitions(SKYRIM_SUPPORT_AE)
set(SKYRIM_SUPPORT_AE ON)
set(SKSE_SUPPORT_XBYAK ON)
#find_package(xbyak CONFIG REQUIRED)
add_subdirectory("lib/CommonLibSSE")
set_target_properties(CommonLibSSE PROPERTIES FOLDER "Libs")  

# Set up projects
function(setup_mod MOD_NAME)
  message(STATUS "Setting up ${MOD_NAME}...")
  add_subdirectory("projects/${MOD_NAME}")
  set_target_properties(${MOD_NAME} PROPERTIES FOLDER "Mods")
endfunction()

setup_mod(ClassicSprintingRedone)
setup_mod(DialogueMovementEnabler)
setup_mod(ExtendedHotkeySystem)
setup_mod(SkyrimSoulsRE)
setup_mod(TimeFormatChanger)