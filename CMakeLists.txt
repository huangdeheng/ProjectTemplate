# Preface
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
IF (COMMAND CMAKE_POLICY)
	CMAKE_POLICY(SET CMP0003 NEW)
	CMAKE_POLICY(SET CMP0005 OLD)
ENDIF (COMMAND CMAKE_POLICY)

PROJECT(THE_PROJECT_NAME)

# Add the 'lib' dir to cmake's module search path
SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/lib/")

# boost
SET(Boost_USE_STATIC_LIBS OFF)
SET(Boost_USE_MULTITHREADED ON)
# Required boost packages
FIND_PACKAGE(Boost 1.38 COMPONENTS date_time filesystem iostreams regex serialization signals system thread REQUIRED)

# Optional boost packages; can build without these.
# Arghhh. Except cmake is treating these as required, not optional. #$%**&
FIND_PACKAGE(Boost 1.38 COMPONENTS python program_options QUIET)
FIND_PACKAGE(Boost 1.38 COMPONENTS math_c99 QUIET)

#IF(Boost_PROGRAM_OPTIONS_FOUND)
#  MESSAGE(STATUS "Found Boost::program_options")
#ELSE(Boost_PROGRAM_OPTIONS_FOUND)
#  MESSAGE(STATUS "Boost program_options missing: needed for Spatial tools (MapTools).")  
#ENDIF(Boost_PROGRAM_OPTIONS_FOUND)

# zeromq
FIND_PACKAGE(ZMQ REQUIRED)

# documentation
FIND_PACKAGE(Doxygen)
IF (DOXYGEN_FOUND)
    # doc dir has no CMakeList.txt
    #ADD_SUBDIRECTORY(doc EXCLUDE_FROM_ALL)
	EXECUTE_PROCESS(
		WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
		COMMAND bzr nick
		OUTPUT_VARIABLE BZR_NICK OUTPUT_STRIP_TRAILING_WHITESPACE)
	EXECUTE_PROCESS(
		WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
		COMMAND bzr revno
		OUTPUT_VARIABLE BZR_REVNO OUTPUT_STRIP_TRAILING_WHITESPACE)
	CONFIGURE_FILE(
		${PROJECT_SOURCE_DIR}/doc/doxygen.conf.in 
		${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
	ADD_CUSTOM_TARGET ( 
		doxygen
		COMMAND ${DOXYGEN} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
		COMMENT "Generating documentation"
	) 
ELSE (DOXYGEN_FOUND)
    MESSAGE(STATUS "Doxygen not found, you won't be able to generate API documentation.")
ENDIF (DOXYGEN_FOUND)

# Specify a common place where CMake should put all executable files 
# (instead of CMAKE_CURRENT_BINARY_DIR)
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/)

# Specify a common place where CMake should put all libraries 
# (instead of CMAKE_CURRENT_BINARY_DIR)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

ADD_SUBDIRECTORY(src)
