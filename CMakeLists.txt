cmake_minimum_required (VERSION 2.6)

# Maps to a solution file (Tutorial.sln). The solution will 
# have all targets (exe, lib, dll) as projects (.vcproj)
project (Tutorial)

# Turn on the ability to create folders to organize projects (.vcproj)
# It creates "CMakePredefinedTargets" folder by default and adds CMake
# defined projects like INSTALL.vcproj and ZERO_CHECK.vcproj
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Set compiler flags and options. 
# Here it is setting the Visual Studio warning level to 4
# set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")

# Command to output information to the console
# Useful for displaying errors, warnings, and debugging
message ("cxx Flags: " ${CMAKE_CXX_FLAGS})

# nope
set(TestVariable "pickles")
message("TestVariable is  <TestVariable>")

message("LANG is " <LANG> ${LANG})

# Sub-directories where more CMakeLists.txt exist
add_subdirectory(app)
add_subdirectory (math)

# Turn on CMake testing capabilities
enable_testing()

# Add test cases
add_test(AppTest1 ${PROJECT_BINARY_DIR}/bin/app 100)
add_test(AppTest2 ${PROJECT_BINARY_DIR}/bin/app 200)
add_test(AppTest3 ${PROJECT_BINARY_DIR}/bin/app 300)

set(AJMCacheVarTest ON CACHE BOOL "A variable to search for in CMakeCache")

include(ExternalProject)

# (assuming your build directory is "build")
# - note that this is not looking for a directory in this repository named "extern"
# - "extern" is not an "output" variable, naturally

# At configure time: (cmake .. in build/)
# - it creates the "build/extern", "build/extern-build", and "build/extern-stamp" directories 
#
# At build time (when you compile Tutorial.sln):
# - it clones the github repository into build/extern-prefix/extern
# - it runs CMakeLists.txt in build/extern
# - It only executes the CMakeLists.txt file but only on build.
# - Note that the external project does NOT share cache variables with this project, I can't find AJMCacheVarTest in CMakeCache in build\extern-prefix\src\extern-build

# TODO: 
# - How does ExternalProject_Add affect CMAKE_MODULE_PATH, if at all?
# - Should try some cpack stuff, remember that NSIS is "unique"... so try everything in NSIS

set(CMAKE_MODULE_PATH "potatoes")
message("BINARY_DIR from the main project is " ${BINARY_DIR})
message("CMAKE_MODULE_PATH before externalproject is " ${CMAKE_MODULE_PATH})
ExternalProject_Add(
                        "extern" # note that this must be the name of the directory, it's NOT a variable to store something in
                        GIT_REPOSITORY "https://github.com/amelvill-umich/Test"
                        CMAKE_ARGS
                            -DBRACKET_BINARY_DIR=<BINARY_DIR>
                            -DVAR_BINARY_DIR=${BINARY_DIR}
                            -DSOMETHING_ELSE="If all else fails, this won't"
)

message("CMAKE_MODULE_PATH after externalproject is " ${CMAKE_MODULE_PATH})
