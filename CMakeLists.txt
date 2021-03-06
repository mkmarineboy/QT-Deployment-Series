cmake_minimum_required(VERSION 3.10)

project(DeploymentProject LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_PREFIX_PATH "/home/muhammet/Qt/5.15.0/gcc_64")

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
