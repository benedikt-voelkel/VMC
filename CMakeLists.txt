############################################################################
# CMakeLists.txt file for building ROOT montecarlo/vmc package
# @author Benedikt Volkel, CERN
# @brief  cmake setup for stand-alone VMC
############################################################################

set(MODULE_NAME "VMC")

# Minimum version of CMake adapted from
# ROOT@2934cb3b9742699ffabde4250b38701471500080
CMAKE_MINIMUM_REQUIRED(VERSION 3.9.0 FATAL_ERROR)

project(${MODULE_NAME})

# Install directories
SET(INSTALL_INC_DIR ${CMAKE_INSTALL_PREFIX}/inc)
SET(INSTALL_LIB_DIR ${CMAKE_INSTALL_PREFIX}/lib)

add_subdirectory(source)
#add_subdirectory(test)
