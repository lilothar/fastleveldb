#check system must be linux  and compiler must be gcc/g++
cmake_minimum_required(VERSION 2.6)
project(fastleveldb)

set(CXX_FLAGS
 -DCHECK_PTHREAD_RETURN_VALUE
 -D_FILE_OFFSET_BITS=64
 -Wall
 -Wextra
 -Werror
 -Wno-unused-parameter
 -Woverloaded-virtual
 -Wpointer-arith
 -Wshadow
 -Wwrite-strings
 -march=native
 -fno-builtin-memcmp
 -MMD 
 -rdynamic
 )
 
if(NOT (${CMAKE_SYSTEM_NAME} STREQUAL "Linux"))
	message(FATAL "not linux system")
endif()

if (CMAKE_COMPILER_IS_GNUCC)
    execute_process(COMMAND ${CMAKE_C_COMPILER} -dumpversion
                    OUTPUT_VARIABLE GCC_VERSION)
    string(REGEX MATCHALL "[0-9]+" GCC_VERSION_COMPONENTS ${GCC_VERSION})
    list(GET GCC_VERSION_COMPONENTS 0 GCC_MAJOR)
    list(GET GCC_VERSION_COMPONENTS 1 GCC_MINOR)

    message(STATUS "GCC_MAJOR " ${GCC_MAJOR})
    message(STATUS "GCC_MINOR" ${GCC_MINOR})
else ()
	message(FATAL "compiler is not gnu")
endif()
#gcc must  >= 4.4
if(${GCC_MAJOR} LESS 4 OR ${GCC_MINOR} LESS 4)
	message(FATAL "gcc vserion is low, requir at least 4.4")	
endif()

include(${CMAKE_ROOT}/Modules/CheckCXXCompilerFlag.cmake) 
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X) 
if (COMPILER_SUPPORTS_CXX0X) 
	set(CXX_FLAGS "${CXX_FLAGS} --std=c++0x")
	message(STATUS "compile with c++0x " "ON") 
else (COMPILER_SUPPORTS_CXX0X)
	message(STATUS "compile not support c++0x ") 
endif(COMPILER_SUPPORTS_CXX0X) 

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

include(${CMAKE_SOURCE_DIR}/cmake/FindSnappy.cmake)
if (SNAPPY_FOUND)    
	message(STATUS "Find snappy include:${SNAPPY_INCLUDE_DIR} libs:${SNAPPY_LIBRARIES}")
	add_definitions("-DSNAPPY")    
	include_directories(${SNAPPY_INCLUDE_DIR})
	link_libraries(${SNAPPY_LIBRARIES})
else (SNAPPY_FOUND)
	message(WARNING "Cannot find snappy, compression is disabled in beehive")
endif (SNAPPY_FOUND)

set(CMAKE_CXX_COMPILER "g++") 
set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g")
set(CMAKE_CXX_FLAGS_RELEASE "-O2")
set(CMAKE_C_COMPILER "g++") 
set(CMAKE_C_FLAGS_DEBUG "-O0 -g")
set(CMAKE_C_FLAGS_RELEASE "-O2")

#build type start 
set(CMAKE_CONFIGURATION_TYPES Debug Release RelWithDebInfo)

if(${BUILD_TYPE} MATCHES "DEBUG") 
	set(CMAKE_BUILD_TYPE Debug)
	option(WALLE_DEBUG "lib debug flag" ON) 
else () 
	set(CMAKE_BUILD_TYPE Release) 
endif() 
	
include_directories(.)
include_directories(include)

set(LIB_SRC
	port/port_posix
	table/block
	table/block_builder
	table/filter_block
	table/format
	table/iterator
	table/merger
	table/table
	table/table_builder
	table/two_level_iterator
	util/arena
	util/bloom
	util/cache
	util/coding
	util/comparator
	util/crc32c
	util/env
	util/env_posix
	util/filter_policy
	util/hash
	util/histogram
	util/logging
	util/options
	util/posix_logger
	util/testharness
	util/testutil
	util/status
	db/c
	db/builder
	db/dbformat
	db/db_impl
	db/db_iter
	db/dumpfile
	db/filename
	db/log_reader
	db/log_writer
	db/memtable
	db/repair
	db/table_cache
	db/version_edit
	db/version_set
	db/write_batch
   )
add_library(fastleveldbShared SHARED ${LIB_SRC}) 
target_link_libraries(fastleveldbShared pthread)

add_library(fastleveldbStatic STATIC ${LIB_SRC}) 
target_link_libraries(fastleveldbStatic pthread)

set_target_properties(fastleveldbShared PROPERTIES OUTPUT_NAME fastleveldb)
set_target_properties(fastleveldbStatic PROPERTIES OUTPUT_NAME fastleveldb)

add_executable(db_bench db/db_bench.cc) 
target_link_libraries(db_bench fastleveldbStatic)

add_executable(dbutil db/leveldb_main.cc) 
target_link_libraries(dbutil fastleveldbStatic)

install(TARGETS fastleveldbShared fastleveldbStatic LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
install(DIRECTORY include/fastleveldb DESTINATION include)

#for test

add_subdirectory(db)
add_subdirectory(util)
add_subdirectory(table)