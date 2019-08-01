cmake_minimum_required(VERSION 3.0)
project(Urg3d 
    VERSION 1.1.0 
    DESCRIPTION "URG3D library"
    LANGUAGES C
    )

add_library(Urg3d SHARED
    src/urg3d_connection.c
    src/urg3d_ring_buffer.c
    src/urg3d_sensor.c
    src/urg3d_tcpclient.c
    src/urg3d_ticks.c
)

add_library(Urg3dStatic STATIC
    src/urg3d_connection.c
    src/urg3d_ring_buffer.c
    src/urg3d_sensor.c
    src/urg3d_tcpclient.c
    src/urg3d_ticks.c
)

set(PUBLIC_HEADERS
    include/urg3d_connection.h
    include/urg3d_detect_os.h
    include/urg3d_errno.h
    include/urg3d_ring_buffer.h
    include/urg3d_sensor.h
    include/urg3d_tcpclient.h
    include/urg3d_ticks.h
)

set_target_properties(Urg3dStatic PROPERTIES OUTPUT_NAME Urg3d)
set_target_properties(Urg3dStatic PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(Urg3dStatic PROPERTIES SOVERSION 1)
target_include_directories(Urg3dStatic PRIVATE include)
target_include_directories(Urg3dStatic PRIVATE src)

set_target_properties(Urg3d PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(Urg3d PROPERTIES SOVERSION 1)
set_target_properties(Urg3d PROPERTIES PUBLIC_HEADER "${PUBLIC_HEADERS}")
target_include_directories(Urg3d PRIVATE include)
target_include_directories(Urg3d PRIVATE src)

if(MSVC)
	target_link_libraries(Urg3d PRIVATE wsock32 WS2_32)
	target_link_libraries(Urg3dStatic PRIVATE wsock32 WS2_32)
endif(MSVC)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(ConfigPackageLocation "${CMAKE_INSTALL_LIBDIR}/cmake/Urg3d")
write_basic_package_version_file(
    "${CMAKE_BINARY_DIR}/cmake/Urg3dConfigVersion.cmake"
    VERSION "${Urg3d_version}"
    COMPATIBILITY SameMajorVersion
)
configure_package_config_file(
    cmake/Urg3dConfig.cmake.in
    cmake/Urg3dConfig.cmake
    INSTALL_DESTINATION ${ConfigPackageLocation}
    PATH_VARS CMAKE_INSTALL_LIBDIR CMAKE_INSTALL_INCLUDEDIR
    NO_CHECK_REQUIRED_COMPONENTS_MACRO
)

configure_file(
    cmake/Urg3d.pc.in 
    cmake/Urg3d.pc @ONLY
)

install(
    TARGETS 
    Urg3d Urg3dStatic
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(
    FILES 
        ${CMAKE_BINARY_DIR}/cmake/Urg3d.pc 
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig
)
install(
    FILES
        ${CMAKE_BINARY_DIR}/cmake/Urg3dConfigVersion.cmake
        ${CMAKE_BINARY_DIR}/cmake/Urg3dConfig.cmake
    DESTINATION ${ConfigPackageLocation}
    COMPONENT devel
)