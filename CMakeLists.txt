cmake_minimum_required(VERSION 3.5)

project(gguf-tools)

add_library(gguflib STATIC fp16.c gguflib.c sds.c)
target_sources(
  gguflib
  PUBLIC FILE_SET HEADERS BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}
         FILES fp16.h gguflib.h sds.h)
target_include_directories(
  gguflib
  PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
         $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
if(MSVC)
  target_compile_options(gguflib PUBLIC /wd4244 /wd4267 /wd4996)
endif()

add_executable(gguf-tools gguf-tools.c)
target_link_libraries(gguf-tools gguflib)

include(GNUInstallDirs)

install(TARGETS gguflib gguf-tools
        EXPORT gguflib-targets
        FILE_SET HEADERS
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(EXPORT gguflib-targets
        FILE gguflib-targets.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/gguflib)

export(EXPORT gguflib-targets
       FILE "${CMAKE_CURRENT_BINARY_DIR}/gguflib-targets.cmake")
