cmake_minimum_required (VERSION 2.6)
project (mysql_udf_randomBlob)
###########################################################
#### uncomment this, if you want to specify own mysql path
###########################################################
#set(MYSQL_PATH "/usr/local/mysql")

if(MYSQL_PATH)
    set(MYSQL_CONFIG "${MYSQL_PATH}/bin/mysql_config")
else()
    find_file(MYSQL_CONFIG mysql_config)
endif()

execute_process(COMMAND ${MYSQL_CONFIG} --include OUTPUT_VARIABLE MYSQL_INCLUDE_DIR)
execute_process(COMMAND ${MYSQL_CONFIG} --libs OUTPUT_VARIABLE MYSQL_LIBRARIES)
execute_process(COMMAND ${MYSQL_CONFIG} --plugindir OUTPUT_VARIABLE MYSQL_PLUGIN_DIR)

STRING(REGEX REPLACE "\n" "" MYSQL_INCLUDE_DIR ${MYSQL_INCLUDE_DIR})
STRING(REGEX REPLACE "\n" "" MYSQL_LIBRARIES ${MYSQL_LIBRARIES})
STRING(REGEX REPLACE "\n" "" MYSQL_PLUGIN_DIR ${MYSQL_PLUGIN_DIR})

set(CMAKE_BUILD_TYPE Debug)
SET(BUILD_SHARED_LIBS ON)

add_definitions(-DMYSQL_DYNAMIC_PLUGIN)
add_definitions(-DDBUG_OFF)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${MYSQL_INCLUDE_DIR} -fPIC -fno-exceptions -fno-rtti")

include_directories ("${PROJECT_SOURCE_DIR}" "${MYSQL_SOURCES_PATH}/include")

file(GLOB FILES_SRC "${PROJECT_SOURCE_DIR}/src/*.h" "${PROJECT_SOURCE_DIR}/src/*.cpp" "${PROJECT_SOURCE_DIR}/src/*.cc")

add_library (mysql_udf_randomBlob ${FILES_SRC})

#get rid of the lib infront of the target file name
set_target_properties(mysql_udf_randomBlob PROPERTIES PREFIX "")

INSTALL(TARGETS mysql_udf_randomBlob DESTINATION "${MYSQL_PLUGIN_DIR}")

message("\nFURTHER INSTALLATION INSTRUCTIONS")
message("---------------------------------\n")
message("After executing make - make install, you need to add the")
message("UDF to MySQL. As MySQL root execute the install_udfs.sql script...\n\n")
