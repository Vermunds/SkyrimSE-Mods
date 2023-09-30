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
 
add_compile_definitions(SKYRIM_SUPPORT_AE)

# Set up CommonLibSSE
message(STATUS "Setting up CommonLibSSE...")
set(SKSE_SUPPORT_XBYAK ON)
#find_package(xbyak CONFIG REQUIRED)
add_subdirectory("lib/CommonLibSSE")
set_target_properties(CommonLibSSE PROPERTIES FOLDER "Libs")  

# Set up projects
message(STATUS "Setting up SkyrimSoulsRE...")
option(SKYRIMSOULSRE_DO_INSTALL "" ON)
set(SKYRIMSOULSRE_INSTALL_PATH "C:/Mod Organizer/mods/SkyrimSoulsRE/SKSE/Plugins" CACHE PATH "")
add_subdirectory("projects/SkyrimSoulsRE")
set_target_properties(SkyrimSoulsRE PROPERTIES FOLDER "Mods")

message(STATUS "Setting up ClassicSprintingRedone...")
option(CLASSICSPRINTING_DO_INSTALL "" ON)
set(CLASSICSPRINTING_INSTALL_PATH "C:/Mod Organizer/mods/ClassicSprintingRedone/SKSE/Plugins" CACHE PATH "")
add_subdirectory("projects/ClassicSprintingRedone")
set_target_properties(ClassicSprintingRedone PROPERTIES FOLDER "Mods")

message(STATUS "Setting up DialogueMovementEnabler...")
option(DIALOGUEMOVEMENTENABLER_DO_INSTALL "" ON)
set(DIALOGUEMOVEMENTENABLER_INSTALL_PATH "C:/Mod Organizer/mods/DialogueMovementEnabler/SKSE/Plugins" CACHE PATH "")
add_subdirectory("projects/DialogueMovementEnabler")
set_target_properties(DialogueMovementEnabler PROPERTIES FOLDER "Mods")

message(STATUS "Setting up TimeFormatChanger...")
option(TIMEFORMATCHANGER_DO_INSTALL "" ON)
set(TIMEFORMATCHANGER_INSTALL_PATH "C:/Mod Organizer/mods/TimeFormatChanger/SKSE/Plugins" CACHE PATH "")
add_subdirectory("projects/TimeFormatChanger")
set_target_properties(TimeFormatChanger PROPERTIES FOLDER "Mods")

message(STATUS "Setting up ExtendedHotkeySystem...")
option(EXTENDEDHOTKEYSYSTEM_DO_INSTALL "" ON)
set(EXTENDEDHOTKEYSYSTEM_INSTALL_PATH "C:/Mod Organizer/mods/ExtendedHotkeySystem/SKSE/Plugins" CACHE PATH "")
add_subdirectory("projects/ExtendedHotkeySystem")
set_target_properties(ExtendedHotkeySystem PROPERTIES FOLDER "Mods")
