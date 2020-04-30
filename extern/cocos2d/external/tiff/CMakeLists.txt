cmake_minimum_required(VERSION 3.6)

set(lib_name tiff)
set(target_name ext_${lib_name})
set(lib_name2 lzma)
set(target_name2 ext_${lib_name2})

project(${lib_name})

include(../cmake/CocosExternalConfig.cmake)

add_library(${target_name} STATIC IMPORTED GLOBAL)
if(X64)
    add_library(${target_name2} STATIC IMPORTED GLOBAL)
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include/${platform_name}"
)
if(WINDOWS)
    set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/lib${lib_name}.lib"
    )
    if(X64)
        set_target_properties(${target_name2} PROPERTIES
        IMPORTED_LOCATION "${platform_spec_path}/${lib_name2}.lib"
        )
    endif()
else()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/lib${lib_name}.a"
  )
endif()
