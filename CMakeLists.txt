cmake_minimum_required(VERSION 3.15.0)
project(Native-Plugin-Template LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED 23)

add_compile_options(
    /EHa
    /utf-8
    -DNDEBUG
    /O2
)

# add_link_options(/DEBUG)
add_compile_definitions(
    NOMINMAX
    UNICODE
)

file(GLOB_RECURSE SOURCES "src/*.cpp")

include_directories(${PROJECT_SOURCE_DIR}/sdk/include)
include_directories(${PROJECT_SOURCE_DIR}/sdk/include/third)

add_library(${PROJECT_NAME} SHARED ${SOURCES})

target_link_libraries(${PROJECT_NAME} PRIVATE
    ${PROJECT_SOURCE_DIR}/sdk/lib/KobeBryant.lib
    ${PROJECT_SOURCE_DIR}/sdk/lib/fmt/fmt.lib
)

add_custom_command(
    TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${PROJECT_SOURCE_DIR}/bin
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${PROJECT_NAME}> ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}/${PROJECT_NAME}.dll
    COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_SOURCE_DIR}/manifest.json ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}/manifest.json
    COMMAND python ${PROJECT_SOURCE_DIR}/scripts/after_build.py ${PROJECT_NAME}
    COMMENT "Packing Plugin ${PROJECT_NAME} to ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}"
    VERBATIM
)
