cmake_minimum_required(VERSION 3.14)

project(TestAWSS3 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if(MSVC)
    string(REPLACE ";" "/aws-cpp-sdk-all;" SYSTEM_MODULE_PATH "${CMAKE_SYSTEM_PREFIX_PATH}/aws-cpp-sdk-all")
    list(APPEND CMAKE_PREFIX_PATH ${SYSTEM_MODULE_PATH})
endif()

list(APPEND CMAKE_PREFIX_PATH "C:\\Program Files\\aws-cpp-sdk-all\\lib\\cmake")

message(STATUS "CMAKE_PREFIX_PATH: ${CMAKE_PREFIX_PATH}")
find_package(AWSSDK REQUIRED COMPONENTS s3)
find_package(QT NAMES Qt6 Qt5 COMPONENTS Core REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core REQUIRED)

add_executable(TestAWSS3
  main.cpp
)

if(MSVC AND BUILD_SHARED_LIBS)
    target_compile_definitions(TestAWSS3 PUBLIC "USE_IMPORT_EXPORT")
    add_definitions(-DUSE_IMPORT_EXPORT)
    list(APPEND SERVICE_LIST s3)
    set(CMAKE_BUILD_TYPE Release)

    #AWSSDK_CPY_DYN_LIBS(SERVICE_LIST "" ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_BUILD_TYPE})
    AWSSDK_CPY_DYN_LIBS(SERVICE_LIST "" ${CMAKE_CURRENT_BINARY_DIR})

    message(STATUS ">>CMAKE_CURRENT_BINARY_DIR: ${CMAKE_CURRENT_BINARY_DIR}")
    message(STATUS ">>CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
    message(STATUS ">>EXECUTABLE_OUTPUT_PATH : ${EXECUTABLE_OUTPUT_PATH}")
endif()

set_compiler_flags(TestAWSS3)
set_compiler_warnings(TestAWSS3)
target_link_libraries(TestAWSS3 PRIVATE ${AWSSDK_LINK_LIBRARIES} PRIVATE Qt${QT_VERSION_MAJOR}::Core)
