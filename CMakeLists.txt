cmake_minimum_required(VERSION 3.24)

project(MedusaModern LANGUAGES C CXX)

option(MEDUSA_BUILD_NETWORK "Build Network shared library" ON)
option(MEDUSA_BUILD_GCQ "Build GCQ shared library (legacy MFC dependency)" OFF)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(MSVC)
  add_compile_options(/W4 /permissive-)
else()
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

set(MEDUSA_ROOT "${CMAKE_CURRENT_SOURCE_DIR}")
set(MEDUSA_THIRDPARTY_DIR "${MEDUSA_ROOT}/ThirdParty")

file(GLOB MEDUSA_AUDIO_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Audio/*.cpp")
file(GLOB MEDUSA_DEBUG_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Debug/*.cpp")
file(GLOB MEDUSA_DISPLAY_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Display/*.cpp")
file(GLOB MEDUSA_DRAW_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Draw/*.cpp")
file(GLOB MEDUSA_FACTORY_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Factory/*.cpp")
file(GLOB MEDUSA_FILE_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/File/*.cpp")
file(GLOB MEDUSA_MATH_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Math/*.cpp")
file(GLOB MEDUSA_MOVIE_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Movie/*.cpp")
file(GLOB MEDUSA_REFLECTION_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Reflection/*.cpp")
file(GLOB MEDUSA_RESOURCE_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Resource/*.cpp")
file(GLOB MEDUSA_STANDARD_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Standard/*.cpp")
file(GLOB MEDUSA_SYSTEM_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/System/*.cpp")
set(MEDUSA_RE2_SRC
  "${MEDUSA_THIRDPARTY_DIR}/re2/frontend.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/memory.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/regerror.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/regexp.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/regsub.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/report.c"
  "${MEDUSA_THIRDPARTY_DIR}/re2/widechar.c"
)

set(MEDUSA_ZLIB_SRC
  "${MEDUSA_THIRDPARTY_DIR}/zlib/adler32.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/compress.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/crc32.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/deflate.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/gzio.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/infblock.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/infcodes.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/inffast.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/inflate.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/inftrees.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/infutil.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/trees.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/uncompr.c"
  "${MEDUSA_THIRDPARTY_DIR}/zlib/zutil.c"
)

set(MEDUSA_SRC
  ${MEDUSA_AUDIO_SRC}
  ${MEDUSA_DEBUG_SRC}
  ${MEDUSA_DISPLAY_SRC}
  ${MEDUSA_DRAW_SRC}
  ${MEDUSA_FACTORY_SRC}
  ${MEDUSA_FILE_SRC}
  ${MEDUSA_MATH_SRC}
  ${MEDUSA_MOVIE_SRC}
  ${MEDUSA_REFLECTION_SRC}
  ${MEDUSA_RESOURCE_SRC}
  ${MEDUSA_STANDARD_SRC}
  ${MEDUSA_SYSTEM_SRC}
  ${MEDUSA_RE2_SRC}
  ${MEDUSA_ZLIB_SRC}
)

add_library(medusa SHARED ${MEDUSA_SRC})
target_include_directories(medusa
  PUBLIC
    "${MEDUSA_ROOT}"
    "${MEDUSA_THIRDPARTY_DIR}"
)
target_compile_definitions(medusa PRIVATE MEDUSA_DLL REGEXP_UNICODE)
set_target_properties(medusa PROPERTIES OUTPUT_NAME "Medusa")

if(MSVC)
  target_compile_definitions(medusa PRIVATE WIN32 _WINDOWS _USRDLL)
endif()

if(MEDUSA_BUILD_NETWORK)
  file(GLOB NETWORK_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/Network/*.cpp")
  list(REMOVE_ITEM NETWORK_SRC
    "${MEDUSA_ROOT}/Network/MirrorServerDepreciated.cpp"
    "${MEDUSA_ROOT}/Network/MirrorServerVersionControl.cpp"
  )
  add_library(network SHARED ${NETWORK_SRC})
  target_include_directories(network PUBLIC "${MEDUSA_ROOT}")
  target_compile_definitions(network PRIVATE NETWORK_DLL)
  target_link_libraries(network PRIVATE medusa)
  set_target_properties(network PROPERTIES OUTPUT_NAME "Network")

  if(WIN32)
    target_link_libraries(network PRIVATE ws2_32)
    target_compile_definitions(network PRIVATE WIN32 _WINDOWS _USRDLL)
  endif()
endif()

if(MEDUSA_BUILD_GCQ)
  message(WARNING "MEDUSA_BUILD_GCQ is experimental and may require MFC configuration.")
  file(GLOB GCQ_SRC CONFIGURE_DEPENDS "${MEDUSA_ROOT}/GCQ/*.cpp")
  add_library(gcq SHARED ${GCQ_SRC})
  target_include_directories(gcq PUBLIC "${MEDUSA_ROOT}" "${MEDUSA_ROOT}/GCQ")
  target_compile_definitions(gcq PRIVATE GCQ_DLL)
  target_link_libraries(gcq PRIVATE medusa network)
  set_target_properties(gcq PROPERTIES OUTPUT_NAME "GCQ")

  if(WIN32)
    target_link_libraries(gcq PRIVATE advapi32 user32)
    target_compile_definitions(gcq PRIVATE WIN32 _WINDOWS _AFXDLL _AFXEXT)
  endif()
endif()
