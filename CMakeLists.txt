cmake_minimum_required(VERSION 3.21)
set(CMAKE_SYSTEM_VERSION 10.0)

# Set up vcpkg
if(DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
      CACHE STRING "")
endif()
set(VCPKG_TARGET_TRIPLET "x64-windows-static-md")

project(
  SkyrimRE
  LANGUAGES CXX
)

# Include guards
if("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
	message(FATAL_ERROR "in-source builds are not allowed")
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
 
# Set up CommonLibSSE
set(SKSE_SUPPORT_XBYAK ON)
find_package(xbyak CONFIG REQUIRED)
add_subdirectory("lib/CommonLibSSE")
set_target_properties(CommonLibSSE PROPERTIES FOLDER "Libs")

# Set up projects
set(SIMPLEINI_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib/simpleini")
set(AUTOTOML_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib/AutoTOML/include")
add_subdirectory("lib/AutoTOML")
set_target_properties(AutoTOML PROPERTIES FOLDER "Libs")

set(SKYRIMSOULSRE_DO_INSTALL ON)
set(SKYRIMSOULSRE_INSTALL_PATH "C:/Mod Organizer/mods/SkyrimSoulsRE/SKSE/Plugins")
add_subdirectory("projects/SkyrimSoulsRE")
set_target_properties(SkyrimSoulsRE PROPERTIES FOLDER "Mods")