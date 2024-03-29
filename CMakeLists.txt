# Version Setup
cmake_minimum_required(VERSION 3.1...3.14.3)

if (${CMAKE_VERSION} VERSION_LESS 3.14)
	cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else()
    cmake_policy(VERSION 3.14)
endif()

# Project Creation
project (Program)
set (CMAKE_CXX_STANDARD 17)
option(BUILD_SHARED_LIBS "Enable compilation of shared libraries" OFF)

# Conan
if(EXISTS ${PROJECT_SOURCE_DIR}/conan/conanbuildinfo.cmake)
    include(${PROJECT_SOURCE_DIR}/conan/conanbuildinfo.cmake)
    conan_basic_setup()
else()
    if(EXISTS ${PROJECT_SOURCE_DIR}/conanfile.txt)
        message( FATAL_ERROR
" A conanfile.txt file exists in the source directory\n"
" indicating conan is used for dependences but a conanbuildinfo.cmake\n"
" file does not exist. Make sure to execute:\n\n"
" conan install ${PROJECT_SOURCE_DIR} --build=missing -if ${CMAKE_BINARY_DIR}" )
    endif()
endif()

# Progam Resource Management
file(COPY ${PROJECT_SOURCE_DIR}/assets DESTINATION ${CMAKE_BINARY_DIR}/bin)

# .exe, Append all src files to an env. var.
FILE(GLOB sources ${PROJECT_SOURCE_DIR}/src/*.cpp
) 
add_executable(${PROJECT_NAME} ${sources} "src/algos.cpp" "src/algos.hpp") # Create executable
set_target_properties(${PROJECT_NAME} PROPERTIES 
	CXX_STANDARD_REQUIRED ON
	CXX_EXTENSIONS OFF
	CXX_STANDARD 17 # Force c++17 standard
)

# Include Directories
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_SOURCE_DIR}/src)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU") 
  target_link_libraries(${PROJECT_NAME} stdc++fs)
endif()
target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS})