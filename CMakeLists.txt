cmake_minimum_required(VERSION 3.22)

project(wingetopt)

option(BUILD_SHARED_LIBS "Build the shared library" OFF)

# set a debug postfix
set(CMAKE_DEBUG_POSTFIX "d")

# default install location vars and configuration build tool
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# modify install targets
set(INSTALL_RUNTIME_DIR ${CMAKE_INSTALL_BINDIR})
set(INSTALL_CONFIG_DIR  ${CMAKE_INSTALL_LIBDIR}/cmake/wingetopt)
set(INSTALL_LIBRARY_DIR ${CMAKE_INSTALL_LIBDIR})
set(INSTALL_INCLUDE_DIR ${CMAKE_INSTALL_INCLUDEDIR})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)

add_definitions(-D_CRT_SECURE_NO_WARNINGS)

if(BUILD_SHARED_LIBS)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
    add_definitions(-DBUILDING_WINGETOPT_DLL -DWINGETOPT_SHARED_LIB)
endif()

add_library(wingetopt src/getopt.c)

# add alias
add_library(wingetopt::wingetopt ALIAS wingetopt)

# set a project config file to enable linking to libraries in the build directory
set(PROJECT_CONFIG_FILE "${CMAKE_BINARY_DIR}/wingetoptConfig.cmake")
configure_package_config_file(wingetoptConfig.cmake.in
    ${PROJECT_CONFIG_FILE}
    INSTALL_DESTINATION ${INSTALL_CONFIG_DIR}
)

# install target and includes
install(TARGETS wingetopt
    EXPORT wingetopt-targets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(FILES src/getopt.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# install config
install(FILES
    ${PROJECT_CONFIG_FILE}
    DESTINATION ${INSTALL_CONFIG_DIR}
)

install(EXPORT wingetopt-targets 
    FILE wingetopt-targets.cmake 
    NAMESPACE wingetopt::
    DESTINATION ${INSTALL_CONFIG_DIR}
)