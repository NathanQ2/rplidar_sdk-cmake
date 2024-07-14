cmake_minimum_required(VERSION 3.20)
project(rplidar_driver)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)

file(GLOB SOURCES_ALL ${PROJECT_SOURCE_DIR}/sdk/src/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/*.h)
file(GLOB_RECURSE SOURCES_DATAUNPACKER ${PROJECT_SOURCE_DIR}/sdk/src/dataunpacker/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/dataunpacker/*.h)
file(GLOB_RECURSE SOURCES_HAL ${PROJECT_SOURCE_DIR}/sdk/src/hal/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/hal/*.h)

list(APPEND SOURCES_ALL ${SOURCES_DATAUNPACKER} ${SOURCES_HAL})

if(WIN32)
    file(GLOB_RECURSE SOURCES_PLATFORM ${PROJECT_SOURCE_DIR}/sdk/src/arch/win32/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/arch/win32/*.h)
    set(PLATFORM WIN32)
elseif(APPLE)
    file(GLOB_RECURSE SOURCES_PLATFORM ${PROJECT_SOURCE_DIR}/sdk/src/arch/macOS/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/arch/macOS/*.h)
    set(PLATFORM APPLE)
elseif(UNIX)
    file(GLOB_RECURSE SOURCES_PLATFORM ${PROJECT_SOURCE_DIR}/sdk/src/arch/linux/*.cpp ${PROJECT_SOURCE_DIR}/sdk/src/arch/linux/*.h)
    set(PLATFORM LINUX)
else()
    message(FATAL_ERROR "Unsupported platform!")
endif()

list(APPEND SOURCES_ALL ${SOURCES_PLATFORM})

#set(LIBRARY_BUILD_PATH ${PROJECT_SOURCE_DIR}/bin/${PLATFORM}/${CMAKE_BUILD_TYPE})

add_library(rplidar_driver STATIC ${SOURCES_ALL})

#set_target_properties(rplidar_driver PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${LIBRARY_BUILD_PATH})

if(WIN32)
    target_compile_definitions(rplidar_driver PRIVATE _WIN32)
elseif(APPLE)
    target_compile_definitions(rplidar_driver PRIVATE _MACOS)
elseif(UNIX)
    target_compile_definitions(rplidar_driver PRIVATE __GNUC__)
endif()


target_include_directories(rplidar_driver PRIVATE sdk/include)
target_include_directories(rplidar_driver PRIVATE sdk/src)
