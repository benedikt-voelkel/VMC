############################################################################
# CMakeLists.txt file for building ROOT montecarlo/vmc package
# @author Benedikt Volkel, CERN
# @brief  cmake setup for stand-alone VMC
############################################################################

# Source directories
SET(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
SET(INC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/inc)

# Library name
SET(LIBRARY_NAME ${MODULE_NAME})

# Required source files to build the library.
set(SRCS
    ${SRC_DIR}/TGeoMCGeometry.cxx
    ${SRC_DIR}/TMCAutoLock.cxx
    ${SRC_DIR}/TMCVerbose.cxx
    ${SRC_DIR}/TVirtualMCApplication.cxx
    ${SRC_DIR}/TVirtualMC.cxx
    ${SRC_DIR}/TVirtualMCGeometry.cxx
    ${SRC_DIR}/TVirtualMCSensitiveDetector.cxx
    ${SRC_DIR}/TVirtualMCStack.cxx
    ${SRC_DIR}/TMCManager.cxx
    ${SRC_DIR}/TMCManagerStack.cxx
    ${SRC_DIR}/TGeoMCBranchArrayContainer.cxx
   )

# Requried headers to build the library.
set(HEADERS
    ${INC_DIR}/TGeoMCGeometry.h
    ${INC_DIR}/TMCAutoLock.h
    ${INC_DIR}/TMCOptical.h
    ${INC_DIR}/TMCParticleType.h
    ${INC_DIR}/TMCProcess.h
    ${INC_DIR}/TMCtls.h
    ${INC_DIR}/TMCVerbose.h
    ${INC_DIR}/TPDGCode.h
    ${INC_DIR}/TVirtualMCApplication.h
    ${INC_DIR}/TVirtualMCGeometry.h
    ${INC_DIR}/TVirtualMC.h
    ${INC_DIR}/TVirtualMCSensitiveDetector.h
    ${INC_DIR}/TVirtualMCStack.h
    ${INC_DIR}/TMCManager.h
    ${INC_DIR}/TMCManagerStack.h
    ${INC_DIR}/TGeoMCBranchArrayContainer.h
    ${INC_DIR}/TMCParticleStatus.h
  )

include_directories(${INC_DIR})


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
SET(ROOT_DICT_LINKDEF_FILE ${INC_DIR}/LinkDef.h)
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
