cmake_minimum_required(VERSION 2.6)
project(nandwebradio)

find_package(PkgConfig)
pkg_check_modules(LIBWEBSOCKETS REQUIRED libwebsockets)
pkg_check_modules(LIBRTLSDR REQUIRED librtlsdr)
include_directories(${LIBWEBSOCKETS_INCLUDE_DIRS} ${LIBRTLSDR_INCLUDE_DIRS})
link_directories(${LIBWEBSOCKETS_LIBRARY_DIRS} ${LIBRTLSDR_LIBRARY_DIRS})

set(CMAKE_CXX_FLAGS "-Wall")
set(SOURCES src/main.c)
add_executable(nandwebradio ${SOURCES})
target_link_libraries(nandwebradio ${LIBWEBSOCKETS_LIBRARIES} ${LIBRTLSDR_LIBRARIES})

set(HTML_FILES 
  ${PROJECT_SOURCE_DIR}/src/html/index.html 
  ${PROJECT_SOURCE_DIR}/src/html/favicon.ico)
foreach(HTML_FILE ${HTML_FILES})
  add_custom_command(TARGET nandwebradio
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:nandwebradio>/share/nandwebradio"
    COMMAND ${CMAKE_COMMAND} -E copy ${HTML_FILE} "$<TARGET_FILE_DIR:nandwebradio>/share/nandwebradio" VERBATIM)
endforeach()

set_property(TARGET nandwebradio
  PROPERTY COMPILE_DEFINITIONS 
  INSTALL_DATADIR="${CMAKE_INSTALL_PREFIX}/share"
  )

install(TARGETS nandwebradio DESTINATION bin)
install(FILES ${HTML_FILES} DESTINATION share/nandwebradio)
