find_package(LibUUID REQUIRED)
find_package(Threads REQUIRED)
find_package(CURL REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(EXPAT REQUIRED)

pkg_check_modules(AVAHI REQUIRED avahi-client)
pkg_check_modules(ENET REQUIRED libenet)

aux_source_directory(./ GAMESTREAM_SRC_LIST)
aux_source_directory(../third_party/h264bitstream GAMESTREAM_SRC_LIST)

aux_source_directory(../third_party/moonlight-common-c/enet MOONLIGHT_COMMON_SRC_LIST)
aux_source_directory(../third_party/moonlight-common-c/src MOONLIGHT_COMMON_SRC_LIST)

add_library(moonlight-common SHARED ${MOONLIGHT_COMMON_SRC_LIST})

add_library(gamestream SHARED ${GAMESTREAM_SRC_LIST})
set_property(TARGET gamestream PROPERTY C_STANDARD 99)

list(APPEND MOONLIGHT_COMMON_DEFINITIONS HAS_SOCKLEN_T)
list(APPEND MOONLIGHT_COMMON_DEFINITIONS HAS_FCNTL)
list(APPEND MOONLIGHT_COMMON_DEFINITIONS NO_MSGAPI)
set_property(TARGET moonlight-common PROPERTY COMPILE_DEFINITIONS ${MOONLIGHT_COMMON_DEFINITIONS})

target_link_libraries(gamestream moonlight-common)

set_target_properties(gamestream PROPERTIES SOVERSION 0 VERSION ${MOONLIGHT_VERSION})
set_target_properties(moonlight-common PROPERTIES SOVERSION 0 VERSION ${MOONLIGHT_VERSION})

target_include_directories(gamestream PRIVATE ../third_party/moonlight-common-c ../third_party/h264bitstream ${AVAHI_INCLUDE_DIRS} ${LIBUUID_INCLUDE_DIRS})
target_include_directories(moonlight-common PRIVATE ../third_party/moonlight-common-c/enet/include)

target_link_libraries(gamestream ${CURL_LIBRARIES} ${OPENSSL_LIBRARIES} ${EXPAT_LIBRARIES} ${AVAHI_LIBRARIES} ${LIBUUID_LIBRARIES})
target_link_libraries(gamestream ${CMAKE_THREAD_LIBS_INIT} ${CMAKE_DL_LIBS})

target_link_libraries(moonlight-common ${ENET_LIBRARIES} ${OPENSSL_LIBRARIES})

install(TARGETS gamestream moonlight-common DESTINATION ${CMAKE_INSTALL_LIBDIR})
