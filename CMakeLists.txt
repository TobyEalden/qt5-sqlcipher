cmake_minimum_required(VERSION 3.0)
project(qsqlcipher)

find_package(Qt5 COMPONENTS Sql REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(SQLCIPHER REQUIRED sqlcipher)

message(STATUS "cipher folders are ${SQLCIPHER_LIBRARIES}, ${SQLCIPHER_INCLUDE_DIRS}")

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

# Arrange output paths so that the plugin is found in the default search path
# relative to the test binary.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/sqldrivers)

option(STATIC "Build plugin as a static library" OFF)
if(STATIC)
    message(STATUS "STATIC BUILD")
    set(LIBTYPE STATIC)
    add_definitions(-DQT_STATICPLUGIN)
    set(TEST_DIR test-static)
else()
    message(STATUS "SHARED BUILD")
    set(LIBTYPE MODULE)
    set(TEST_DIR test-shared)
endif()

add_library(qsqlcipher ${LIBTYPE}
    smain.cpp
    qt-private/qsql_sqlite.cpp
)

target_include_directories(qsqlcipher PRIVATE
    ${Qt5Sql_PRIVATE_INCLUDE_DIRS}
    ${SQLCIPHER_INCLUDE_DIRS}
)

set(FIXED_SQLCIPHER_LIBRARIES "/usr/local/lib/libsqlcipher.a")
target_link_libraries(qsqlcipher
    Qt5::Sql
    ${FIXED_SQLCIPHER_LIBRARIES}
    ${OPENSSL_LIBRARIES}
)

include(CTest)
if(BUILD_TESTING)
    add_subdirectory(${TEST_DIR})
endif()
