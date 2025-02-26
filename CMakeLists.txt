#
# Copyright (c) Contributors to the Open 3D Engine Project. For complete copyright and license terms please see the LICENSE at the root of this distribution.
# 
# SPDX-License-Identifier: Apache-2.0 OR MIT
#
#

#! Adds the --project-path argument to the VS IDE debugger command arguments
function(add_vs_debugger_arguments)
    # Inject the project root into the --project-path argument into the Visual Studio Debugger arguments by defaults
    list(APPEND app_targets NetSoakTest.GameLauncher NetSoakTest.ServerLauncher)
    list(APPEND app_targets AssetBuilder AssetProcessor AssetProcessorBatch Editor)
    foreach(app_target IN LISTS app_targets)
        if (TARGET ${app_target})
            set_property(TARGET ${app_target} APPEND PROPERTY VS_DEBUGGER_COMMAND_ARGUMENTS "--project-path=\"${CMAKE_CURRENT_LIST_DIR}\"")
        endif()
    endforeach()
endfunction()

if(NOT PROJECT_NAME)
    cmake_minimum_required(VERSION 3.19)
    project(NetSoakTest
        LANGUAGES C CXX
        VERSION 1.0.0.0
    )
    include(EngineFinder.cmake OPTIONAL)
    find_package(o3de REQUIRED)
    o3de_initialize()
    add_vs_debugger_arguments()
else()
    # Add the project_name to global LY_PROJECTS_TARGET_NAME property
    file(READ "${CMAKE_CURRENT_LIST_DIR}/project.json" project_json)

    string(JSON project_target_name ERROR_VARIABLE json_error GET ${project_json} "project_name")
    if(json_error)
        message(FATAL_ERROR "Unable to read key 'project_name' from 'project.json'")
    endif()

    set_property(GLOBAL APPEND PROPERTY LY_PROJECTS_TARGET_NAME ${project_target_name})

    add_subdirectory(Gem)
endif()
