############################################################################
# CMakeLists.txt file for building ROOT montecarlo/vmc package
# @author Benedikt Volkel, CERN
# @brief  cmake setup for stand-alone VMC
############################################################################

set(MODULE_NAME "VMC")

# Minimum version of CMake
# TODO Determine minimal CMake version
CMAKE_MINIMUM_REQUIRED(VERSION 3.11.0 FATAL_ERROR)

project(${MODULE_NAME})

# Install directories
SET(INSTALL_INC_DIR ${CMAKE_INSTALL_PREFIX}/include)
SET(INSTALL_LIB_DIR ${CMAKE_INSTALL_PREFIX}/lib)

# Source directories
SET(IMP_SRC_DIR ${CMAKE_SOURCE_DIR}/src)
SET(INC_SRC_DIR ${CMAKE_SOURCE_DIR}/inc)

# Library name
SET(LIBRARY_NAME ${MODULE_NAME})

# Required source files to build the library.
set(SRCS
    ${IMP_SRC_DIR}/TGeoMCGeometry.cxx
    ${IMP_SRC_DIR}/TMCAutoLock.cxx
    ${IMP_SRC_DIR}/TMCVerbose.cxx
    ${IMP_SRC_DIR}/TVirtualMCApplication.cxx
    ${IMP_SRC_DIR}/TVirtualMC.cxx
    ${IMP_SRC_DIR}/TVirtualMCGeometry.cxx
    ${IMP_SRC_DIR}/TVirtualMCSensitiveDetector.cxx
    ${IMP_SRC_DIR}/TVirtualMCStack.cxx
    ${IMP_SRC_DIR}/TMCManager.cxx
    ${IMP_SRC_DIR}/TMCManagerStack.cxx
    ${IMP_SRC_DIR}/TGeoMCBranchArrayContainer.cxx
   )

# Requried headers to build the library.
set(HEADERS
    ${INC_SRC_DIR}/TGeoMCGeometry.h
    ${INC_SRC_DIR}/TMCAutoLock.h
    ${INC_SRC_DIR}/TMCOptical.h
    ${INC_SRC_DIR}/TMCParticleType.h
    ${INC_SRC_DIR}/TMCProcess.h
    ${INC_SRC_DIR}/TMCtls.h
    ${INC_SRC_DIR}/TMCVerbose.h
    ${INC_SRC_DIR}/TPDGCode.h
    ${INC_SRC_DIR}/TVirtualMCApplication.h
    ${INC_SRC_DIR}/TVirtualMCGeometry.h
    ${INC_SRC_DIR}/TVirtualMC.h
    ${INC_SRC_DIR}/TVirtualMCSensitiveDetector.h
    ${INC_SRC_DIR}/TVirtualMCStack.h
    ${INC_SRC_DIR}/TMCManager.h
    ${INC_SRC_DIR}/TMCManagerStack.h
    ${INC_SRC_DIR}/TGeoMCBranchArrayContainer.h
    ${INC_SRC_DIR}/TMCParticleStatus.h
  )

include_directories(${INC_SRC_DIR})


########
# ROOT #
########
# TODO Need to provide more sophisticated and robust lookup
list(APPEND CMAKE_PREFIX_PATH $ENV{ROOTSYS})
find_package(ROOT REQUIRED COMPONENTS Core EG Geom MathCore Physics)
include(${ROOT_USE_FILE})
# Find ROOT headers
include_directories(${ROOT_INCLUDE_DIRS})

# Generate ROOT dictionary
SET(ROOT_DICT_LINKDEF_FILE ${INC_SRC_DIR}/LinkDef.h)
SET(ROOT_DICT_NAME "G__${LIBRARY_NAME}")

ROOT_GENERATE_DICTIONARY(${ROOT_DICT_NAME} ${HEADERS} LINKDEF ${ROOT_DICT_LINKDEF_FILE})
# Files produced by the dictionary generation
SET(ROOT_DICT_LIB_FILES
    "${PROJECT_BINARY_DIR}/lib${LIBRARY_NAME}_rdict.pcm"
    "${PROJECT_BINARY_DIR}/lib${LIBRARY_NAME}.rootmap"
)

# Build a library from the sources specified above together with generated ROOT
# dictionary
add_library(${LIBRARY_NAME} SHARED ${SRCS} "${ROOT_DICT_NAME}.cxx" ${HEADERS})

# Link together with ROOT libs
target_link_libraries(${LIBRARY_NAME} ${ROOT_LIBRARIES})

# Install headers
install(FILES ${HEADERS} DESTINATION ${INSTALL_INC_DIR})
# Install libraries
install(TARGETS ${MODULE_NAME} DESTINATION ${INSTALL_LIB_DIR})
# Install the ROOT dictionary files
install(FILES ${ROOT_DICT_LIB_FILES} DESTINATION ${INSTALL_LIB_DIR})
