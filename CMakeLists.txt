cmake_minimum_required(VERSION 3.24)

set(CMAKE_CXX_STANDARD_REQUIRED ON)
if ("${GEODE_TARGET_PLATFORM}" STREQUAL "iOS")
    add_definitions(-DGLES_SILENCE_DEPRECATION)
endif()
set(CMAKE_CXX_VISIBILITY_PRESET hidden)

project(PlatformerSaves VERSION 1.1.3)

file(GLOB_RECURSE SOURCES
    src/ui/*.cpp
    src/hooks/*.cpp
    src/managers/*.cpp
    src/domain/*.cpp
    src/util/*.cpp
    src/*.cpp
)

add_library(${PROJECT_NAME} SHARED ${SOURCES})

target_compile_definitions(${PROJECT_NAME} PUBLIC
    $<$<CONFIG:Debug>:PS_DEBUG>
    $<$<CONFIG:RelWithDebInfo>:PS_DEBUG>
    $<$<CONFIG:Release>:PS_RELEASE>
    $<$<CONFIG:MinSizeRel>:PS_RELEASE>
)

target_compile_options(${PROJECT_NAME} PRIVATE "-Wno-microsoft-include")
target_compile_options(${PROJECT_NAME} PRIVATE "-Wno-invalid-offsetof")
target_include_directories(${PROJECT_NAME} PRIVATE src/)

# This line MUST come right before include() to pass the platform variables
set(GEODE_TARGET_PLATFORM ${GEODE_TARGET_PLATFORM})

include($ENV{GEODE_SDK}/cmake/GeodeMod.cmake)

add_subdirectory(PersistenceAPI)

setup_geode_mod(${PROJECT_NAME} DEPENDS sabe.persistenceapi)
