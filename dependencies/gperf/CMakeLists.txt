cmake_minimum_required(VERSION 3.10.2)

project(external-gperf)
include(ExternalProject)

message(STATUS "Preparing external project \"gperf\"")

ExternalProject_add(
    gperf
    URL http://ftp.gnu.org/pub/gnu/gperf/gperf-3.1.tar.gz
    PREFIX gperf
    CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND make
    )
