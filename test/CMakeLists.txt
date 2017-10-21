cmake_minimum_required ( VERSION 2.6 )

project( micro-ecc )
set( CMAKE_VERBOSE_MAKEFILE OFF )

option (HAVING_TESTS " Compiles examples " ON)
message( "-- -DHAVING_TESTS Compiles examples DEFAULT: ${HAVING_TESTS}\n\n")

################################################################################
# set( CMAKE_BUILD_TYPE Debug )
if ( NOT CMAKE_BUILD_TYPE )
  set( CMAKE_BUILD_TYPE Release )
endif ()


# message("CMAKE_SOURCE_DIR : ${CMAKE_SOURCE_DIR}")

add_definitions(-DuECC_OPTIMIZATION_LEVEL=3)

if  ( NOT BUILD_SHARED_LIB )
  set(lib_FLAGS "-fvisibility=hidden")
endif ()

set(lib_FLAGS
    "${lib_FLAGS} -Wall -Wextra -Wshadow -Werror \
     -Wno-missing-field-initializers -Wno-unused-parameter \
     -Wno-comment -Wno-unused -Wno-unknown-pragmas")

set(lib_CFLAGS
    "-std=c99")

set(lib_CXXFLAGS
    "-std=c++11 -Wno-reorder -fno-rtti -fno-exceptions")

set(lib_INCLUDES
    ../)

set(lib_SOURCES 
    ../uECC.c ../uECC.h ../types.h)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${lib_FLAGS} ${lib_CFLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${lib_FLAGS} ${lib_CXXFLAGS}")

message("CMAKE_C_FLAGS   : ${CMAKE_C_FLAGS}")
message("CMAKE_CXX_FLAGS : ${CMAKE_CXX_FLAGS}")

add_definitions( -DuECC_SUPPORTS_secp160r1=1 )
add_definitions( -DuECC_SUPPORTS_secp192r1=1 )
add_definitions( -DuECC_SUPPORTS_secp224r1=1 )
add_definitions( -DuECC_SUPPORTS_secp256r1=1 )
add_definitions( -DuECC_SUPPORTS_secp256k1=1 )
add_definitions( -DuECC_SUPPORT_COMPRESSED_POINT=1 )

include_directories(${lib_INCLUDES})

if  ( BUILD_SHARED_LIB )
add_library(uECC SHARED ${lib_SOURCES})
else ()
add_library(uECC STATIC ${lib_SOURCES})
endif ()

install(TARGETS uECC DESTINATION lib)

if ( HAVING_TESTS )
  add_executable(test_compress test_compress.c)
  target_link_libraries(test_compress uECC)
  install(TARGETS test_compress DESTINATION bin)

  add_executable(test_compute test_compute.c)
  target_link_libraries(test_compute uECC)
  install(TARGETS test_compute DESTINATION bin)

  add_executable(test_ecdh test_ecdh.c)
  target_link_libraries(test_ecdh uECC)
  install(TARGETS test_ecdh DESTINATION bin)

  add_executable(test_ecdsa test_ecdsa.c)
  target_link_libraries(test_ecdsa uECC)
  install(TARGETS test_ecdsa DESTINATION bin)
endif ()
