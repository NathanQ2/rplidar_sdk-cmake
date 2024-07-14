cmake_minimum_required(VERSION 3.20)
project(rplidar_sdk)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)

file(GLOB SOURCES sdk/src/*.cpp sdk/src/*.h)
file(GLOB_RECURSE SOURCES_DATAUNPACKER sdk/dataunpacker/*.cpp sdk/dataunpacker/*.h)
file(GLOB_RECURSE SOURCES_HAL sdk/hal/*.cpp sdk/hal/*.h)

set(PLATFORM Unkown)

if(WIN32)
    file(GLOB_RECURSE SOURCES_PLATFORM sdk/arch/win32/*.cpp sdk/arch/win32/*.h)
    set(PLATFORM WIN32)
elseif(APPLE)
    file(GLOB_RECURSE SOURCES_PLATFORM sdk/arch/macOS/*.cpp sdk/arch/macOS/*.h)
    set(PLATFORM APPLE)
elseif(LINUX)
    file(GLOB_RECURSE SOURCES_PLATFORM sdk/arch/linux/*.cpp sdk/arch/linux/*.h)
    set(PLATFORM LINUX)
endif()

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/${PLATFORM}/${CMAKE_BUILD_TYPE})

add_library(rplidar_sdk STATIC ${SOURCES} ${SOURCES_DATAUNPACKER} ${SOURCES_HAL} ${SOURCES_PLATFORM})

if(WIN32)
    target_compile_definitions(rplidar_sdk PRIVATE _WIN32)
elseif(APPLE)
    target_compile_definitions(rplidar_sdk PRIVATE _MACOS)
elseif(LINUX)
    target_compile_definitions(rplidar_sdk PRIVATE __GNUC__)
endif()


target_include_directories(rplidar_sdk PRIVATE sdk/include)
target_include_directories(rplidar_sdk PRIVATE sdk/src)
