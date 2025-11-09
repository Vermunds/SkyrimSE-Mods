cmake_minimum_required(VERSION 3.31)
set(CMAKE_SYSTEM_VERSION 10.0)

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

# Global compile definitions
if (MSVC)
	add_compile_definitions(
		_UNICODE
	)

	if (NOT ${CMAKE_GENERATOR} STREQUAL "Ninja")
		add_compile_options(
			/MP	# Build with Multiple Processes
		)
	endif ()
endif ()

# Set up CommonLibSSE
message(STATUS "Setting up CommonLibSSE...")
add_compile_definitions(SKYRIM_SUPPORT_AE)
set(ENABLE_SKYRIM_VR OFF)
set(ENABLE_SKYRIM_SE OFF)
set(ENABLE_SKYRIM_AE ON)
set(SKSE_SUPPORT_XBYAK ON)
set(BUILD_TESTS OFF)
#find_package(xbyak CONFIG REQUIRED)
add_subdirectory("lib/CommonLibSSE")
set_target_properties(CommonLibSSE PROPERTIES FOLDER "Libs")

# Set up projects
function(setup_mod MOD_NAME)
    message(STATUS "Setting up ${MOD_NAME}...")
    add_subdirectory("projects/${MOD_NAME}")

    set_target_properties(${MOD_NAME} PROPERTIES FOLDER "Mods")

    target_compile_features(
	    ${MOD_NAME}
	    PRIVATE
		    cxx_std_23
    )

	target_compile_options(
		${MOD_NAME}
		PRIVATE
			/sdl	# Enable Additional Security Checks
			/utf-8	# Set Source and Executable character sets to UTF-8
			/Zi	    # Debug Information Format

			/permissive-	# Standards conformance

			# Warning -> error
			/we4715	# 'function' : not all control paths return a value

			# Disable warnings
			/wd4458  # declaration hides class member
			/wd4100 # unreferenced formal parameter

			# Disable warnings from external libraries
			/external:anglebrackets
			/external:W0

			/W4	# Warning level

			"$<$<CONFIG:DEBUG>:>"
			"$<$<CONFIG:RELEASE>:/Zc:inline;/JMC-;/Ob3>"
	)

	target_link_options(
		${MOD_NAME}
		PRIVATE
			#/WX	# Treat Linker Warnings as Errors

			"$<$<CONFIG:DEBUG>:/INCREMENTAL;/OPT:NOREF;/OPT:NOICF>"
			"$<$<CONFIG:RELEASE>:/INCREMENTAL:NO;/OPT:REF;/OPT:ICF;/DEBUG:FULL>"
	)

	if(DEFINED SKYRIM_DATA_PATH)
		set(${MOD_NAME}_OUTPUT_DIR "${SKYRIM_DATA_PATH}/SKSE/Plugins")
	elseif(DEFINED MO_MODS_FOLDER_PATH)
		set(${MOD_NAME}_OUTPUT_DIR "${MO_MODS_FOLDER_PATH}/${MOD_NAME}/SKSE/Plugins")
	endif()

	if (DEFINED ${MOD_NAME}_OUTPUT_DIR)
		set_target_properties(${MOD_NAME} PROPERTIES
			RUNTIME_OUTPUT_DIRECTORY "${${MOD_NAME}_OUTPUT_DIR}"
			CMAKE_COMPILE_PDB_OUTPUT_DIRECTORY "${${MOD_NAME}_OUTPUT_DIR}"
		)
	else()
		message(STATUS "No install path specified for mod ${MOD_NAME}. Skipping post-build copy command.")
	endif()

endfunction()

setup_mod(ClassicSprintingRedone)
setup_mod(DialogueMovementEnabler)
setup_mod(ExtendedHotkeySystem)
setup_mod(SkyrimSoulsRE)
setup_mod(TimeFormatChanger)