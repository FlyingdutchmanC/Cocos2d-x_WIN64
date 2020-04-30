#/**
# * \file CMakeLists.txt
# * \date 2019/11/08 11:15
# *
# * \author Alexander Puzicha
# * Contact: alexander.puzicha@cs.tu-dortmund.de
# *
# * \brief This is the configuration and build file for this project.
# *
# * Copyright (c) 2019 Alexander Puzicha 
# * Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"),
# * to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense,
# * and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: 
# * The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software. 
# * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
# * DEALINGS IN THE SOFTWARE. 
# *
#*/

cmake_minimum_required(VERSION 3.6)


set(APP_NAME Win64App)

project(${APP_NAME})
set (CMAKE_CXX_STANDARD 11)


set(USE_OPENMP False)

set(COCOS2DX_ROOT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/extern/cocos2d)
set(CMAKE_MODULE_PATH ${COCOS2DX_ROOT_PATH}/cmake/Modules/)
set(EXTERN_PATH ${CMAKE_CURRENT_SOURCE_DIR}/extern)


include(CocosBuildSet)
add_subdirectory(${COCOS2DX_ROOT_PATH}/cocos ${ENGINE_BINARY_PATH}/cocos/core)

if(WIN32 OR X64)
	set(CMAKE_EXE_LINKER_FLAGS_DEBUG "${CMAKE_EXE_LINKER_FLAGS_DEBUG} /NODEFAULTLIB:msvcrt /NODEFAULTLIB:libcmt")
	set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} /NODEFAULTLIB:libcmt")
endif()


if(LINUX)
	set(LINK_FLAGS "${LINK_FLAGS} -no-pie")
	SET(GCC_COVERAGE_LINK_FLAGS  "-no-pie")
	SET(CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS}")
	message(STATUS "Linker flags for linux")
endif()

# record sources, headers, resources...
set(GAME_SOURCE)
set(GAME_HEADER)


set(GAME_RES_FOLDER
    "${CMAKE_CURRENT_SOURCE_DIR}/Resources"
    )
if(APPLE OR WINDOWS)
    cocos_mark_multi_resources(common_res_files RES_TO "Resources" FOLDERS ${GAME_RES_FOLDER})
endif()


# add cross-platforms source files and header files 
list(APPEND GAME_SOURCE
     Classes/AppDelegate.cpp
     Classes/HelloWorldScene.cpp
     )

list(APPEND GAME_HEADER
     Classes/AppDelegate.h
     Classes/HelloWorldScene.h
     )


if(LINUX)
    list(APPEND GAME_SOURCE
         proj.linux/main.cpp
         )
elseif(WINDOWS)
    list(APPEND GAME_HEADER
         proj.win32/main.h
         proj.win32/resource.h
         )
    list(APPEND GAME_SOURCE
         proj.win32/main.cpp
         proj.win32/game.rc
         ${common_res_files}
         )
endif()

# mark app complie info and libs info
set(all_code_files
    ${GAME_HEADER}
    ${GAME_SOURCE}
    )


# Linking
add_executable(${APP_NAME} ${all_code_files})

target_link_libraries(${APP_NAME}
${CMAKE_THREAD_LIBS_INIT}
cocos2d
)

target_include_directories(${APP_NAME}
        PRIVATE Classes
        PRIVATE ${COCOS2DX_ROOT_PATH}/cocos/audio/include/
)

# mark app resources
setup_cocos_app_config(${APP_NAME})
if(WINDOWS)
    cocos_copy_target_dll(${APP_NAME} COPY_TO ${APP_RES_DIR}/..)
endif()

if(LINUX OR WINDOWS)
    cocos_copy_res(COPY_TO ${APP_RES_DIR} FOLDERS ${GAME_RES_FOLDER})
endif()