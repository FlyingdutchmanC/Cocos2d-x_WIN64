cmake_minimum_required(VERSION 3.6)

set(lib_name bullet)
set(target_name ext_${lib_name})

project(${lib_name})

include(../cmake/CocosExternalConfig.cmake)

add_library(${target_name} STATIC IMPORTED GLOBAL)

 if(X64)
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include/win64"
    )
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include/win64/bullet"
    )
else()
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
    )
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include/bullet"
    )
endif()

if(WINDOWS)

    if(X64)
        set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/LinearMath.lib"
  )

  # multi libs
    add_library(bullet3common STATIC IMPORTED)
  set_target_properties(bullet3common PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/Bullet3Common.lib"
  )
  add_library(bulletcollision STATIC IMPORTED)
  set_target_properties(bulletcollision PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/BulletCollision.lib"
  )
  add_library(bulletdynamics STATIC IMPORTED)
  set_target_properties(bulletdynamics PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/BulletDynamics.lib"
  )
  add_library(bulletsoftbody STATIC IMPORTED)
  set_target_properties(bulletsoftbody PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/BulletSoftBody.lib"
  )
  add_library(bulletinversedynamics STATIC IMPORTED)
  set_target_properties(bulletinversedynamics PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/BulletInverseDynamics.lib"
  )

   add_library(linearmath STATIC IMPORTED)
  set_target_properties(linearmath PROPERTIES
  IMPORTED_LOCATION "${platform_spec_path}/LinearMath.lib"
  )

  # internal link depend
  set_property(TARGET bullet3common APPEND PROPERTY INTERFACE_LINK_LIBRARIES "linearmath")
  set_property(TARGET bulletcollision APPEND PROPERTY INTERFACE_LINK_LIBRARIES "linearmath")
  set_property(TARGET bulletdynamics APPEND PROPERTY INTERFACE_LINK_LIBRARIES "bulletcollision")

  # mark multi libs link into single one: "${target_name}"
    set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bullet3common"
  )

  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletcollision"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletdynamics"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletsoftbody"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletinversedynamics"
  )
    else()
      set_target_properties(${target_name} PROPERTIES
        IMPORTED_LOCATION "${platform_spec_path}/lib${lib_name}.lib"
      )
    endif()
else()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libLinearMath.a"
  )

  # multi libs
  add_library(bulletcollision STATIC IMPORTED)
  set_target_properties(bulletcollision PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libBulletCollision.a"
  )
  add_library(bulletdynamics STATIC IMPORTED)
  set_target_properties(bulletdynamics PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libBulletDynamics.a"
  )
  add_library(bulletmultithreaded STATIC IMPORTED)
  set_target_properties(bulletmultithreaded PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libBulletMultiThreaded.a"
  )
  add_library(minicl STATIC IMPORTED)
  set_target_properties(minicl PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libMiniCL.a"
  )
  add_library(linearmath STATIC IMPORTED)
  set_target_properties(linearmath PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/libLinearMath.a"
  )

  # internal link depend
  set_property(TARGET bulletcollision APPEND PROPERTY INTERFACE_LINK_LIBRARIES "linearmath")
  set_property(TARGET bulletdynamics APPEND PROPERTY INTERFACE_LINK_LIBRARIES "bulletcollision")

  # mark multi libs link into single one: "${target_name}"
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletcollision"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletdynamics"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "bulletmultithreaded"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "minicl"
  )
endif()


