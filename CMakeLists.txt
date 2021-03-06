project(pvr.mythtv)

cmake_minimum_required(VERSION 2.8.12)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR})

enable_language(CXX)
enable_language(C)

find_package(Kodi REQUIRED)
find_package(kodiplatform REQUIRED)
find_package(p8-platform REQUIRED)

include_directories(${kodiplatform_INCLUDE_DIRS}
                    ${p8-platform_INCLUDE_DIRS}
                    ${KODI_INCLUDE_DIR}
                    ${PROJECT_SOURCE_DIR}/lib/cppmyth/src)

add_definitions(-DUSE_DEMUX -D__STDC_FORMAT_MACROS)

if(NOT WIN32)
  add_options(ALL_LANGUAGES ALL_BUILDS "-fPIC -Wall")
endif()

set(MYTHTV_SOURCES src/avinfo.cpp
                   src/categories.cpp
                   src/demuxer/ES_AAC.cpp
                   src/demuxer/ES_MPEGAudio.cpp
                   src/demuxer/bitstream.cpp
                   src/demuxer/ES_Teletext.cpp
                   src/demuxer/debug.cpp
                   src/demuxer/ES_Subtitle.cpp
                   src/demuxer/ES_h264.cpp
                   src/demuxer/ES_AC3.cpp
                   src/demuxer/elementaryStream.cpp
                   src/demuxer/ES_MPEGVideo.cpp
                   src/demuxer/ES_hevc.cpp
                   src/demuxer/tsDemuxer.cpp
                   src/demux.cpp
                   src/pvrclient-mythtv.cpp
                   src/cppmyth/MythScheduleManager.cpp
                   src/cppmyth/MythScheduleHelperNoHelper.cpp
                   src/cppmyth/MythScheduleHelper75.cpp
                   src/cppmyth/MythScheduleHelper76.cpp
                   src/cppmyth/MythScheduleHelper85.cpp
                   src/cppmyth/MythEPGInfo.cpp
                   src/cppmyth/MythRecordingRule.cpp
                   src/cppmyth/MythProgramInfo.cpp
                   src/cppmyth/MythChannel.cpp
                   src/client.cpp
                   src/fileOps.cpp
                   src/filestreaming.cpp)

add_subdirectory(lib/cppmyth)

set(DEPLIBS ${p8-platform_LIBRARIES} cppmyth)
if(WIN32)
  list(APPEND DEPLIBS ws2_32)
else()
  list(APPEND DEPLIBS m)
endif()

if(NOT WIN32 AND (CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX))
  if(HAS_BUILTIN_SYNC_ADD_AND_FETCH)
    add_options(ALL_LANGUAGES ALL_BUILDS "-DHAS_BUILTIN_SYNC_ADD_AND_FETCH")
  endif()
  if(HAS_BUILTIN_SYNC_SUB_AND_FETCH)
    add_options(ALL_LANGUAGES ALL_BUILDS "-DHAS_BUILTIN_SYNC_SUB_AND_FETCH")
  endif()
endif()

set(BUILD_SHARED_LIBS ON)
build_addon(pvr.mythtv MYTHTV DEPLIBS)

include(CPack)
