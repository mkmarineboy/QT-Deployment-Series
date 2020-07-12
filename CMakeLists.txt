cmake_minimum_required(VERSION 3.10)

project(DeploymentProject LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_PREFIX_PATH "/home/muhammet/Qt/5.15.0/gcc_64")
set(PROJECT_VERSION 1.0.1)

find_package(Qt5 COMPONENTS Core Quick REQUIRED)

configure_file("${CMAKE_SOURCE_DIR}/deployment/deployment.json.in" "${CMAKE_BINARY_DIR}/deployment.json")

add_executable(DeploymentProject
  main.cpp
  qml.qrc
)

target_compile_definitions(DeploymentProject
  PRIVATE $<$<OR:$<CONFIG:Debug>,$<CONFIG:RelWithDebInfo>>:QT_QML_DEBUG>)
target_link_libraries(DeploymentProject
  PRIVATE Qt5::Core Qt5::Quick)

add_custom_command(TARGET ${CMAKE_PROJECT_NAME}
                   POST_BUILD
                   COMMAND cqtdeployer -confFile "${CMAKE_BINARY_DIR}/deployment.json")

install(DIRECTORY "${CMAKE_BINARY_DIR}/DeploymentPackage/" DESTINATION "/opt/${CMAKE_PROJECT_NAME}")
set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_NAME "${CMAKE_PROJECT_NAME}")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Muhammet Kucuk")
set(CPACK_DEBIAN_FILE_NAME "${CMAKE_PROJECT_NAME}.deb")
set(CPACK_DEBIAN_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_DEBIAN_PACKAGE_HOMEPAGE "https://muhammetkucuk.com")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Debian package test with CPack")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Test for Deb")
set(CPACK_DEBIAN_PACKAGE_CONTROL_EXTRA
    "${CMAKE_SOURCE_DIR}/deployment/prerm;${CMAKE_SOURCE_DIR}/deployment/postinst")
include(CPack)
