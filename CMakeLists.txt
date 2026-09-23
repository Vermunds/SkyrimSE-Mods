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

# Set up ModConfigUI
message(STATUS "Setting up ModConfigUI...")
add_subdirectory("lib/ModConfigUI")

# Set up projects
function(setup_mod_target MOD_NAME MOD_FOLDER_NAME)
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

	get_mod_data_dir(MOD_DATA_DIR ${MOD_FOLDER_NAME})

	if (NOT MOD_DATA_DIR)
		message(STATUS "No install path specified for mod ${MOD_NAME}. Skipping post-build copy command.")
	endif()

endfunction()

# The root of the mod's Data layout in the deployment target, empty when no install path is set.
function(get_mod_data_dir OUT_VAR MOD_FOLDER_NAME)
	if(DEFINED SKYRIM_DATA_PATH)
		set(${OUT_VAR} "${SKYRIM_DATA_PATH}" PARENT_SCOPE)
	elseif(DEFINED MO_MODS_FOLDER_PATH)
		set(${OUT_VAR} "${MO_MODS_FOLDER_PATH}/${MOD_FOLDER_NAME}" PARENT_SCOPE)
	else()
		set(${OUT_VAR} "" PARENT_SCOPE)
	endif()
endfunction()

# Installs a mod into the deployment target: the binaries the build produced.
#
# The build keeps its own output in the build folder and only the finished files are copied over, so
# the intermediate ones (.ilk, vc140.pdb) never end up in the game folder.
function(setup_mod_deploy MOD_NAME)
	get_mod_data_dir(MOD_DATA_DIR ${MOD_NAME})

	if (NOT MOD_DATA_DIR)
		return()
	endif()

	set(DEPLOY_COMMANDS
		COMMAND ${CMAKE_COMMAND} -E make_directory "${MOD_DATA_DIR}/SKSE/Plugins"
	)

	# The binaries and their debug symbols. Every target of the mod goes to SKSE/Plugins.
	foreach(MOD_TARGET ${ARGN})
		list(APPEND DEPLOY_COMMANDS
			COMMAND ${CMAKE_COMMAND} -E copy_if_different "$<TARGET_FILE:${MOD_TARGET}>" "${MOD_DATA_DIR}/SKSE/Plugins"
			COMMAND ${CMAKE_COMMAND} -E copy_if_different "$<TARGET_PDB_FILE:${MOD_TARGET}>" "${MOD_DATA_DIR}/SKSE/Plugins"
		)
	endforeach()

	# Part of the default build and depends on the mod, so a build always deploys what it just produced.
	add_custom_target(
		${MOD_NAME}_Deploy ALL
		${DEPLOY_COMMANDS}
		COMMENT "Deploying ${MOD_NAME} to ${MOD_DATA_DIR}..."
		VERBATIM
	)
	set_target_properties(${MOD_NAME}_Deploy PROPERTIES FOLDER "Deploy")
	add_dependencies(${MOD_NAME}_Deploy ${ARGN})
endfunction()

function(setup_mod MOD_NAME)
    message(STATUS "Setting up ${MOD_NAME}...")

    # A project defines a single target named after its folder, unless it sets
    # MOD_TARGETS (PARENT_SCOPE) to the list of targets it wants set up.
    set(MOD_TARGETS ${MOD_NAME})
    add_subdirectory("projects/${MOD_NAME}")

    foreach(MOD_TARGET ${MOD_TARGETS})
        setup_mod_target(${MOD_TARGET} ${MOD_NAME})
    endforeach()

    # One deployment per mod, no matter how many targets it builds.
    setup_mod_deploy(${MOD_NAME} ${MOD_TARGETS})
endfunction()

setup_mod(ClassicSprintingRedone)
setup_mod(CleanSaveAutoReloader)
setup_mod(DialogueMovementEnabler)
setup_mod(ExtendedHotkeySystem)
setup_mod(SkyrimSoulsRE)
setup_mod(TimeFormatChanger)