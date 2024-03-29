#-----------------------------------------------------------------------------
# Does Xdmf require support for 64 bit file systems
INCLUDE(${CMAKE_CURRENT_SOURCE_DIR}/CheckCXXSourceRuns.cmake)
FILE(READ "${CMAKE_CURRENT_SOURCE_DIR}/vtkRequireLargeFilesSupport.cxx"
  XDMF_REQUIRE_LARGE_FILE_SUPPORT_FILE)
CHECK_CXX_SOURCE_RUNS("${XDMF_REQUIRE_LARGE_FILE_SUPPORT_FILE}"
  CMAKE_REQUIRE_LARGE_FILE_SUPPORT "Xdmf support for 64 bit file systems")
SET(XDMF_REQUIRE_LARGE_FILE_SUPPORT ${CMAKE_REQUIRE_LARGE_FILE_SUPPORT})
# This Needs to be done since Xdmf/CMakeLists.txt != PV/Utils/Xdmf2/CMakeLists.txt
# Because INSTALL_* commands require a leading / and because INSTALL (cmake 2.4
# and newer) requires no leading / to install under INSTALL_PREFIX, we
# are stripping the leading /. In the future, there should be no leading
# / in any install directory variables
STRING(REGEX REPLACE "^/" "" VTK_INSTALL_LIB_DIR_CM24 "${XDMF_INSTALL_LIB_DIR}")
STRING(REGEX REPLACE "^/" "" VTK_INSTALL_BIN_DIR_CM24 "${XDMF_INSTALL_BIN_DIR}")
STRING(REGEX REPLACE "^/" "" VTK_INSTALL_INCLUDE_DIR_CM24 "${XDMF_INSTALL_INCLUDE_DIR}")
STRING(REGEX REPLACE "^/" "" VTK_INSTALL_PACKAGE_DIR_CM24 "${XDMF_LIB_INCLUDE_DIR}/vtk")
#
SET(XDMF_INSTALL_LIB_DIR_CM24 ${VTK_INSTALL_LIB_DIR_CM24})
SET(XDMF_INSTALL_BIN_DIR_CM24 ${VTK_INSTALL_BIN_DIR_CM24})
SET(XDMF_INSTALL_INCLUDE_DIR_CM24 ${VTK_INSTALL_INCLUDE_DIR_CM24})
SET(XDMF_INSTALL_PACKAGE_DIR_CM24 ${VTK_INSTALL_PACKAGE_DIR_CM24})

IF(NOT XDMF_INSTALL_EXPORT_NAME)
  SET(XDMF_INSTALL_EXPORT_NAME XdmfTargets)
ENDIF()

SET( XdmfFormatSource
  XdmfArray
  XdmfDOM
  XdmfDataDesc
  XdmfExpression
  XdmfValues
  XdmfValuesXML
  XdmfValuesHDF
  XdmfValuesBinary
  XdmfHeavyData
  XdmfHDF
  XdmfHDFSupport
  XdmfLightData
  XdmfInformation
  XdmfDataItem
  XdmfDataStructure
  XdmfDataTransform
  XdmfElement
  XdmfObject
  )

SET( XdmfExtraSource
  XdmfExprLex
  XdmfExprYacc
  )

SET( XdmfModelSource
  XdmfRoot
  XdmfDomain
  XdmfAttribute
  XdmfGrid
  XdmfTopology
  XdmfTime
  XdmfRegion
  XdmfSet
  XdmfMap
  XdmfGeometry
  XdmfH5Driver
  XdmfDsm
  XdmfDsmBuffer
  XdmfDsmComm
  XdmfDsmMsg
  )

SET(CSHARP_SOURCES 
	SWIGTYPE_p_double.cs
	SWIGTYPE_p_float.cs
	SWIGTYPE_p_hid_t.cs
	SWIGTYPE_p_int.cs
	SWIGTYPE_p_istream.cs
	SWIGTYPE_p_long_long.cs
	SWIGTYPE_p_ostream.cs
	SWIGTYPE_p_p__xmlDoc.cs
	SWIGTYPE_p_void.cs
	SWIGTYPE_p__xmlNode.cs
	XdmfArray.cs
	XdmfArrayList.cs
	XdmfAttribute.cs
	XdmfCSharp.cs
	XdmfCSharpPINVOKE.cs
	XdmfDataDesc.cs
	XdmfDataItem.cs
	XdmfDataStructure.cs
	XdmfDomain.cs
	XdmfDOM.cs
	XdmfDsmComm.cs
	XdmfDsmCommMpi.cs
	XdmfDsm.cs
	XdmfElement.cs
	XdmfGeometry.cs
	XdmfGrid.cs
	XdmfHDF.cs
	XdmfHeavyData.cs
	XdmfInformation.cs
	XdmfLightData.cs
	XdmfMap.cs
	XdmfObject.cs
	XdmfRegion.cs
	XdmfRoot.cs
	XdmfSet.cs
	XdmfTime.cs
	XdmfTopology.cs
	XdmfValues.cs
	XdmfValuesHDF.cs
	XdmfValuesXML.cs
 	XdmfDsmBuffer.cs
	XdmfDsmMsg.cs
	XdmfOpenCallback.cs
	XdmfCloseCallback.cs
	XdmfReadCallback.cs
	XdmfWriteCallback.cs 
	)

SET( XdmfGzipSource
  gzstream
   )

IF(XDMF_BUILD_VTK)
  IF(NOT VTK_USE_SYSTEM_ZLIB)
    ADD_DEFINITIONS(-DXDMF_USE_VTK_ZLIB)
  ENDIF(NOT VTK_USE_SYSTEM_ZLIB)
ENDIF(XDMF_BUILD_VTK)

# IF(XDMF_BUILD_MPI)
#     SET(XdmfModelSource ${XdmfModelSource} XdmfH5Driver)
# ENDIF(XDMF_BUILD_MPI) 

SET( XdmfMpiSource
  XdmfDsmCommMpi
  )

SET( XdmfMySQLSource
    XdmfValuesMySQL
    )

SET( XdmfPythonSource
  XdmfPython
  )

SET( XdmfPythonSourceNoMpi
  XdmfPythonNoMpi
  )

IF(XDMF_BUILD_MPI)
  SET(XDMF_PYTHON_FILE ${Xdmf_SOURCE_DIR}/libsrc/Xdmf.py)
ELSE(XDMF_BUILD_MPI)
  SET(XDMF_PYTHON_FILE ${Xdmf_SOURCE_DIR}/libsrc/XdmfNoMpi.py)
ENDIF(XDMF_BUILD_MPI)

OPTION(XDMF_REGENERATE_WRAPPERS
  "Regenerate XdmfPython.cxx" OFF)
MARK_AS_ADVANCED(XDMF_REGENERATE_WRAPPERS)
IF(XDMF_REGENERATE_WRAPPERS)
  FIND_PROGRAM(SWIG_EXECUTABLE 
    NAMES swig-1.3 swig
    PATHS /usr/bin
    DOC "Swig executable for generating wrapped files")
  MARK_AS_ADVANCED(SWIG_EXECUTABLE)
  IF(SWIG_EXECUTABLE)
    SET(XDMF_SWIG_INCLUDES 
      -I${Xdmf_SOURCE_DIR} 
      -I${Xdmf_SOURCE_DIR}/libsrc 
      -I${Xdmf_BINARY_DIR}/libsrc 
      -I${Xdmf_SOURCE_DIR}/Ice/libsrc
      -I${Xdmf_BINARY_DIR}/Ice/libsrc
      )
    GET_FILENAME_COMPONENT(SWIG_EXE_PATH ${SWIG_EXECUTABLE}
      PATH)
    SET(SWIG_LIB_PATH "${SWIG_EXE_PATH}/Lib")
    # MESSAGE("SWIG_LIB_PATH ${SWIG_LIB_PATH}")
    IF(EXISTS "${SWIG_LIB_PATH}")
      SET(XDMF_SWIG_INCLUDES
        ${XDMF_SWIG_INCLUDES}
        -I${SWIG_LIB_PATH}
        -I${SWIG_LIB_PATH}/tcl
        -I${SWIG_LIB_PATH}/python
        )
    ENDIF(EXISTS "${SWIG_LIB_PATH}")
    FOREACH(LANGUAGE Python)
      STRING(TOLOWER ${LANGUAGE} lang)
#      SET(swigtarget ${Xdmf_BINARY_DIR}/libsrc/Xdmf${LANGUAGE}.cxx)
      IF(XDMF_BUILD_MPI)
        SET(swigtarget ${Xdmf_BINARY_DIR}/libsrc/Xdmf${LANGUAGE}.cxx)
        ADD_CUSTOM_COMMAND(
         OUTPUT ${swigtarget}
         DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/Xdmf.i
         COMMAND ${SWIG_EXECUTABLE}
         ARGS -v -c++ -make_default -includeall "-${lang}" ${swig_extra_args} ${XDMF_SWIG_INCLUDES} -o "${swigtarget}" Xdmf.i)
      ELSE(XDMF_BUILD_MPI) 
        SET(swigtarget ${Xdmf_BINARY_DIR}/libsrc/Xdmf${LANGUAGE}NoMpi.cxx)
        ADD_CUSTOM_COMMAND(
         OUTPUT ${swigtarget}
         DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/Xdmf.i
         COMMAND ${SWIG_EXECUTABLE}
         ARGS -v -c++ -DXDMF_NO_MPI -make_default -includeall "-${lang}" ${swig_extra_args} ${XDMF_SWIG_INCLUDES} -o "${swigtarget}" Xdmf.i)
      ENDIF(XDMF_BUILD_MPI) 

    ENDFOREACH(LANGUAGE)
    ADD_CUSTOM_TARGET(RerunSwig DEPENDS 
      ${Xdmf_BINARY_DIR}/libsrc/XdmfPython.cxx
      )
  ENDIF(SWIG_EXECUTABLE)
  SET(XDMF_PYTHON_FILE ${Xdmf_BINARY_DIR}/libsrc/Xdmf.py)
ENDIF(XDMF_REGENERATE_WRAPPERS)


OPTION(XDMF_REGENERATE_YACCLEX
  "Regenerate XdmfExprYacc.cxx and XdmfExprLex.cxx" OFF)
MARK_AS_ADVANCED(XDMF_REGENERATE_YACCLEX)
IF(XDMF_REGENERATE_YACCLEX)
  FIND_PROGRAM(YACC_EXECUTABLE
    NAMES yacc bison
    PATHS /usr/bin
    DOC "Yacc or Bison executable")
  FIND_PROGRAM(FLEX_EXECUTABLE
    NAMES flex
    PATHS /usr/bin
    DOC "Flex executable")
  MARK_AS_ADVANCED(YACC_EXECUTABLE FLEX_EXECUTABLE)
  IF(YACC_EXECUTABLE)
    SET(BISON_FLAGS)
    IF(YACC_EXECUTABLE MATCHES "bison")
      SET(BISON_FLAGS "-y")
    ENDIF(YACC_EXECUTABLE MATCHES "bison")
    ADD_CUSTOM_COMMAND(
      OUTPUT ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.tab.c
      DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.y
      COMMAND ${YACC_EXECUTABLE}
      ARGS ${BISON_FLAGS} -d -b ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc
      -p dice_yy
      ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.y
      )
    ADD_CUSTOM_COMMAND(
      OUTPUT ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.tab.h
      DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.y
      COMMAND ${YACC_EXECUTABLE}
      ARGS ${BISON_FLAGS} -d -b ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc
      -p dice_yy
      ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.y
      )
    ADD_CUSTOM_COMMAND(
      OUTPUT ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.cxx
      DEPENDS ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.tab.c
      COMMAND ${CMAKE_COMMAND}
      ARGS -E copy ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.tab.c
      ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.cxx
      )
    ADD_CUSTOM_TARGET(RerunYacc DEPENDS
      ${Xdmf_BINARY_DIR}/libsrc/XdmfExprYacc.cxx)
  ENDIF(YACC_EXECUTABLE)
  IF(FLEX_EXECUTABLE)
    ADD_CUSTOM_COMMAND(
      OUTPUT ${Xdmf_BINARY_DIR}/libsrc/XdmfExprLex.cxx
      DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.l
      COMMAND ${FLEX_EXECUTABLE}
      ARGS -Pdice_yy -o${Xdmf_BINARY_DIR}/libsrc/XdmfExprLex.cxx 
      ${Xdmf_SOURCE_DIR}/libsrc/XdmfExpr.l)
    ADD_CUSTOM_TARGET(RerunLex DEPENDS
      ${Xdmf_BINARY_DIR}/libsrc/XdmfExprLex.cxx)
  ENDIF(FLEX_EXECUTABLE)

ENDIF(XDMF_REGENERATE_YACCLEX)


IF (VTK_USE_MPI)
  # Needed for mpich 2
  ADD_DEFINITIONS("-DMPICH_IGNORE_CXX_SEEK")
ENDIF (VTK_USE_MPI)

INCLUDE (${CMAKE_ROOT}/Modules/CheckTypeSize.cmake)
IF(WIN32)
  CHECK_TYPE_SIZE(__int64        SIZEOF___INT64)
ENDIF(WIN32)
CHECK_TYPE_SIZE("long long"    SIZEOF_LONG_LONG)

INCLUDE (${CMAKE_ROOT}/Modules/CheckIncludeFile.cmake)
CHECK_INCLUDE_FILE(malloc.h XDMF_HAVE_MALLOC_H)

# System to Build
STRING(REGEX REPLACE "-" "_" XDMF_SYSTEM ${CMAKE_SYSTEM_NAME})

SET(ARCH_TO_BUILD ${XDMF_SYSTEM} CACHE INTERNAL "Host Arcitecture : Linux IRIXN32 IRIX64 AIX CYGWIN")
# Allow the user to customize their build with some local options
#
ADD_DEFINITIONS(-D${ARCH_TO_BUILD})

SET(XDMF_SIZEOF_CHAR   ${VTK_SIZEOF_CHAR})
SET(XDMF_SIZEOF_DOUBLE ${VTK_SIZEOF_DOUBLE})
SET(XDMF_SIZEOF_FLOAT  ${VTK_SIZEOF_FLOAT})
SET(XDMF_SIZEOF_INT    ${VTK_SIZEOF_INT})
SET(XDMF_SIZEOF_LONG   ${VTK_SIZEOF_LONG})
SET(XDMF_SIZEOF_SHORT  ${VTK_SIZEOF_SHORT})
INCLUDE (${CMAKE_ROOT}/Modules/CheckTypeSize.cmake)
INCLUDE (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
IF(WIN32)
  CHECK_TYPE_SIZE(__int64        SIZEOF___INT64)
ENDIF(WIN32)
CHECK_TYPE_SIZE("long long"    SIZEOF_LONG_LONG)
CHECK_FUNCTION_EXISTS(strtoll HAVE_STRTOLL)

# Don't Really Need to check these ...
SET(XDMF_VOID_VALID 1)
SET(XDMF_VOID "void")
SET(XDMF_PTR_VALID 1)
SET(XDMF_PTR "void *")
SET(XDMF_CHAR_VALID 1)
SET(XDMF_CHAR "char")
SET(XDMF_8_INT_VALID 1)
SET(XDMF_8_INT  "char")
SET(XDMF_8_U_INT  "unsigned char")
SET(XDMF_16_INT_VALID 1)
SET(XDMF_16_INT "short")
SET(XDMF_16_U_INT "unsigned short")
# These should be Valid
IF(${XDMF_SIZEOF_FLOAT} MATCHES 4)
  SET(XDMF_32_FLOAT_VALID 1)
  SET(XDMF_FLOAT "float")
  SET(XDMF_32_FLOAT "float")
ELSE (${XDMF_SIZEOF_FLOAT} MATCHES 4)
  MESSAGE(SEND_ERROR "Can't Find a 32 Bit Float")
ENDIF (${XDMF_SIZEOF_FLOAT} MATCHES 4)
IF(${XDMF_SIZEOF_DOUBLE} MATCHES 8)
  SET(XDMF_64_FLOAT_VALID 1)
  SET(XDMF_DOUBLE "double")
  SET(XDMF_64_FLOAT "double")
ELSE (${XDMF_SIZEOF_DOUBLE} MATCHES 8)
  MESSAGE(SEND_ERROR "Can't Find a 64 Bit Float")
ENDIF (${XDMF_SIZEOF_DOUBLE} MATCHES 8)
# These are sometimes different
IF(${XDMF_SIZEOF_INT} MATCHES 4)
  SET(XDMF_32_INT_VALID 1)
  SET(XDMF_32_INT "int")
  SET(XDMF_32_U_INT "unsigned int")
  SET(XDMF_32_S_INT "int")
ELSE (${XDMF_SIZEOF_INT} MATCHES 4)
  IF(${XDMF_SIZEOF_LONG} MATCHES 4)
    SET(XDMF_32_INT_VALID 1)
    SET(XDMF_32_INT "long")
    SET(XDMF_32_U_INT "unsigned long")
    SET(XDMF_32_S_INT "long")
  ELSE(${XDMF_SIZEOF_LONG} MATCHES 4)
    MESSAGE(SEND_ERROR "Can't Find a 32 Bit Integer")
  ENDIF(${XDMF_SIZEOF_LONG} MATCHES 4)
ENDIF(${XDMF_SIZEOF_INT} MATCHES 4)

INCLUDE(${Xdmf_SOURCE_DIR}/CMake/CheckFor64BitStreams.cmake)
CHECK_FOR_64BIT_STREAMS(XDMF_HAVE_64BIT_STREAMS)

# Find include files
FIND_PATH(XDMF_HAVE_FCNTL fcntl.h /usr/include /usr/include/sys)
FIND_PATH(XDMF_HAVE_NETINET in.h /usr/include/netinet /usr/include /usr/include/sys)
FIND_PATH(XDMF_HAVE_MMAN mman.h /usr/include/sys /usr/include)
MARK_AS_ADVANCED(XDMF_HAVE_FCNTL XDMF_HAVE_NETINET XDMF_HAVE_MMAN)


##
CONFIGURE_FILE(${Xdmf_SOURCE_DIR}/libsrc/XdmfConfig.h.in
  ${Xdmf_BINARY_DIR}/libsrc/XdmfConfig.h)

IF(NOT WIN32)
  ADD_DEFINITIONS(-D_HPUX_SOURCE)
ENDIF(NOT WIN32)

SET(XdmfSources ${XdmfFormatSource} ${XdmfExtraSource} ${XdmfModelSource})

OPTION(XDMF_USE_MYSQL
    "Build Support for MySQL DataItems" OFF)
MARK_AS_ADVANCED(XDMF_USE_MYSQL)
IF(XDMF_USE_MYSQL)
    FIND_PATH(MYSQL_INCLUDE_PATH
        mysql.h
        /usr/include
        /usr/include/mysql
        /usr/local/include
        /usr/local/include/mysql)
    FIND_LIBRARY(MYSQL_CLIENT_LIBRARY
        mysqlclient
        /usr/lib
        /usr/lib/mysql
        /usr/local/lib
        /usr/local/lib/mysql)
    SET(XdmfSources ${XdmfSources} ${XdmfMySQLSource})
    ADD_DEFINITIONS("-DXDMF_USE_MYSQL")
    INCLUDE_DIRECTORIES(${MYSQL_INCLUDE_PATH})
ENDIF(XDMF_USE_MYSQL)
# gzip
OPTION(XDMF_USE_GZIP "Build GZip Compression" OFF)
IF(XDMF_USE_GZIP)
SET(XdmfSources ${XdmfSources} ${XdmfGzipSource})
    ADD_DEFINITIONS("-DXDMF_USE_GZIP")
ENDIF(XDMF_USE_GZIP)
# bz2lib
OPTION(XDMF_USE_BZIP2 "Use bzip2" OFF)
IF(XDMF_USE_BZIP2)
  FIND_PACKAGE(BZip2)
  SET(XDMF_BZIP2_LIBRARIES ${BZIP2_LIBRARIES})
  SET(XDMF_BZIP2_INCLUDE_DIR ${BZIP2_INCLUDE_DIR})
  INCLUDE_DIRECTORIES(${BZIP2_INCLUDE_DIR})
  ADD_DEFINITIONS("-DXDMF_USE_BZIP2")
ENDIF(XDMF_USE_BZIP2)

## MPI ###
IF(XDMF_BUILD_MPI)
    SET(XdmfSources ${XdmfSources} ${XdmfMpiSource})
ELSE(XDMF_BUILD_MPI)
    ADD_DEFINITIONS("-DXDMF_NO_MPI")
ENDIF(XDMF_BUILD_MPI)

IF(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)
  SET(LIBTYPE SHARED)
ELSE(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)
  SET(LIBTYPE STATIC)
ENDIF(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)

ADD_LIBRARY(Xdmf ${LIBTYPE} ${XdmfSources})

IF(XDMF_USE_MYSQL)
    ADD_DEFINITIONS("-DXDMF_USE_MYSQL")
    INCLUDE_DIRECTORIES(${MYSQL_INCLUDE_PATH})
    TARGET_LINK_LIBRARIES(Xdmf ${MYSQL_CLIENT_LIBRARY} )
ENDIF(XDMF_USE_MYSQL)

IF(XDMF_BUILD_MPI)
  IF(MPI_EXTRA_LIBRARY)
    TARGET_LINK_LIBRARIES(Xdmf ${MPI_EXTRA_LIBRARY})
  ENDIF(MPI_EXTRA_LIBRARY)
  TARGET_LINK_LIBRARIES(Xdmf ${MPI_LIBRARY} )
ENDIF(XDMF_BUILD_MPI)

TARGET_LINK_LIBRARIES(Xdmf 
  ${HDF5_LIBRARY} 
  ${XDMF_MPI_LIBRARIES} 
  ${LIBXML2_LIBRARY} 
  ${XDMF_ZLIB_LIBRARIES}
  ${XDMF_BZIP2_LIBRARIES})

OPTION(XDMF_WRAP_PYTHON "Build a Python wrapping interface" OFF)
SET (temp_VTK_PYTHON FALSE)
IF (VTK_WRAP_PYTHON AND NOT PARAVIEW_ENABLE_PYTHON)
  SET (temp_VTK_PYTHON TRUE)
ENDIF (VTK_WRAP_PYTHON AND NOT PARAVIEW_ENABLE_PYTHON)

IF(XDMF_WRAP_PYTHON OR temp_VTK_PYTHON)
  FIND_PACKAGE(PythonInterp)
  FIND_PACKAGE(PythonLibs)
ENDIF(XDMF_WRAP_PYTHON OR temp_VTK_PYTHON)

IF(XDMF_WRAP_PYTHON)
  IF(NOT BUILD_SHARED_LIBS)
    MESSAGE("Python wrapping only works on shared libraries")
  ENDIF(NOT BUILD_SHARED_LIBS)
  INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})
  ADD_DEFINITIONS(-DSWIG_TYPE_TABLE=Xdmf)
  IF(XDMF_BUILD_MPI)
    ADD_LIBRARY(_Xdmf MODULE ${XdmfPythonSource})
  ELSE(XDMF_BUILD_MPI)
    ADD_LIBRARY(_Xdmf MODULE ${XdmfPythonSourceNoMpi})
  ENDIF(XDMF_BUILD_MPI)
  TARGET_LINK_LIBRARIES(_Xdmf Xdmf ${HDF5_LIBRARY} ${XDMF_ZLIB_LIBRARIES} ${XDMF_BZIP2_LIBRARIES})
  IF(WIN32 OR APPLE)
    TARGET_LINK_LIBRARIES(_Xdmf ${PYTHON_LIBRARY})
  ENDIF(WIN32 OR APPLE)

  # Get the install path for python modules
  if(NOT XDMF_WRAP_PYTHON_INSTALL_DIR)
    EXEC_PROGRAM("${PYTHON_EXECUTABLE}" "${Xdmf_SOURCE_DIR}/CMake/"
      ARGS "setup_install_paths.py" "purelib" --prefix=""
      OUTPUT_VARIABLE XDMF_WRAP_PYTHON_INSTALL_DIR)

    SET(XDMF_WRAP_PYTHON_INSTALL_DIR ${XDMF_WRAP_PYTHON_INSTALL_DIR}/Xdmf)
  endif()

  # Replace backslashes with forward slashes
  IF(WIN32)
    STRING(REGEX REPLACE "\\\\" "/" XDMF_WRAP_PYTHON_INSTALL_DIR
      "${XDMF_WRAP_PYTHON_INSTALL_DIR}")
  ENDIF(WIN32)

  SET_TARGET_PROPERTIES(_Xdmf PROPERTIES PREFIX "")
  ADD_DEPENDENCIES(_Xdmf Xdmf)
  
  SET(XDMF_PYTHON_INIT_COMMANDS "from Xdmf import *\n")
  IF(XDMF_BUILD_UTILS)
    SET(XDMF_PYTHON_INIT_COMMANDS "${XDMF_PYTHON_INIT_COMMANDS}from XdmfUtils import *\n")
  ENDIF(XDMF_BUILD_UTILS)

  IF(XDMF_BUILD_VTK)
    IF(VTK_WRAP_PYTHON)
      SET(XDMF_PYTHON_INIT_COMMANDS "${XDMF_PYTHON_INIT_COMMANDS}from libvtkXdmfPython import *\n")
    ENDIF(VTK_WRAP_PYTHON)
  ENDIF(XDMF_BUILD_VTK)

  CONFIGURE_FILE(__init__.py ${Xdmf_BINARY_DIR}/libsrc/__init__.py @ONLY)
  CONFIGURE_FILE(${XDMF_PYTHON_FILE} ${CMAKE_BINARY_DIR}/libsrc/Xdmf.py COPYONLY)

  INSTALL(FILES ${Xdmf_BINARY_DIR}/libsrc/__init__.py 
    DESTINATION ${XDMF_WRAP_PYTHON_INSTALL_DIR}
    COMPONENT Runtime)
  INSTALL(FILES ${XDMF_PYTHON_FILE}
      DESTINATION ${XDMF_WRAP_PYTHON_INSTALL_DIR}
      COMPONENT Runtime
      RENAME Xdmf.py) # always named the same regardless of build with or without mpi

  # Compile Byte Code
  INSTALL(CODE "EXECUTE_PROCESS(COMMAND ${PYTHON_EXECUTABLE} ${Xdmf_SOURCE_DIR}/CMake/CompileXdmfByteCode.py 
    ${CMAKE_INSTALL_PREFIX}/${XDMF_WRAP_PYTHON_INSTALL_PY_DIR})")
  
ENDIF(XDMF_WRAP_PYTHON)

OPTION(XDMF_WRAP_CSHARP "Build a C# wrapping interface" OFF)
IF(XDMF_WRAP_CSHARP)
  IF(NOT BUILD_SHARED_LIBS)
    MESSAGE("C# wrapping only works on shared libraries")
  ENDIF(NOT BUILD_SHARED_LIBS)

  FIND_PROGRAM(SWIG_EXECUTABLE 
    NAMES swig-1.3 swig
    PATHS /usr/bin
    DOC "Swig executable for generating wrapped files")
  MARK_AS_ADVANCED(SWIG_EXECUTABLE)
  SET(SWIG_WRAPPER_CXX ${Xdmf_BINARY_DIR}/libsrc/XdmfCSharp.cxx)
  IF(SWIG_EXECUTABLE)
    SET(XDMF_SWIG_INCLUDES 
      -I${Xdmf_SOURCE_DIR} 
      -I${Xdmf_SOURCE_DIR}/libsrc 
      -I${Xdmf_BINARY_DIR}/libsrc 
      -I${Xdmf_SOURCE_DIR}/Ice/libsrc
      -I${Xdmf_BINARY_DIR}/Ice/libsrc
      )
    GET_FILENAME_COMPONENT(SWIG_EXE_PATH ${SWIG_EXECUTABLE}
      PATH)
    SET(SWIG_LIB_PATH "${SWIG_EXE_PATH}/Lib")
    IF(EXISTS "${SWIG_LIB_PATH}")
      SET(XDMF_SWIG_INCLUDES
        ${XDMF_SWIG_INCLUDES}
        -I${SWIG_LIB_PATH}
        )
    ENDIF(EXISTS "${SWIG_LIB_PATH}")
	IF(XDMF_BUILD_MPI)
	  ADD_CUSTOM_COMMAND(
	   OUTPUT ${SWIG_WRAPPER_CXX}
	   DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/Xdmf.i
	   COMMAND ${SWIG_EXECUTABLE}
	   ARGS -module XdmfCSharp -namespace Xdmf  -v -c++ -make_default -includeall "-csharp" ${swig_extra_args} ${XDMF_SWIG_INCLUDES} -o "${SWIG_WRAPPER_CXX}"  Xdmf.i)
	ELSE(XDMF_BUILD_MPI) 
	  ADD_CUSTOM_COMMAND(
	   OUTPUT ${SWIG_WRAPPER_CXX}
	   DEPENDS ${Xdmf_SOURCE_DIR}/libsrc/Xdmf.i
	   COMMAND ${SWIG_EXECUTABLE}
	   ARGS -module XdmfCSharp -namespace Xdmf   -v -c++ -DXDMF_NO_MPI -make_default -includeall "-csharp" ${swig_extra_args} ${XDMF_SWIG_INCLUDES} -o "${SWIG_WRAPPER_CXX}" Xdmf.i)
	ENDIF(XDMF_BUILD_MPI) 
	
    ADD_CUSTOM_TARGET(RerunSwigCSharp DEPENDS 
      ${SWIG_WRAPPER_CXX}
      )

  ELSE(SWIG_EXECUTABLE)
    MESSAGE("Wrappers generation is disabled, because SWIG executable is not found")
  ENDIF(SWIG_EXECUTABLE)

  ADD_LIBRARY(XdmfCSharp SHARED ${SWIG_WRAPPER_CXX})
  TARGET_LINK_LIBRARIES(XdmfCSharp Xdmf XdmfUtils ${HDF5_LIBRARY} ${XDMF_ZLIB_LIBRARIES})
  ADD_CUSTOM_COMMAND (TARGET XdmfCSharp
		      POST_BUILD
		      COMMAND csc -target:library -out:../bin/XmdfCSharpBindings.dll ${CSHARP_SOURCES}
		      WORKING_DIRECTORY ${XDMF_INSTALL_DIR})
  ADD_DEPENDENCIES(XdmfCSharp Xdmf XdmfUtils )
ENDIF(XDMF_WRAP_CSHARP)

IF(NOT XDMF_INSTALL_NO_DEVELOPMENT)
  FILE(GLOB devFiles RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.h")
  INSTALL(
      FILES ${devFiles}
      DESTINATION ${XDMF_INSTALL_INCLUDE_DIR_CM24}
	  COMPONENT Development)
  INSTALL(
      FILES ${Xdmf_BINARY_DIR}/libsrc/XdmfConfig.h
      DESTINATION ${XDMF_INSTALL_INCLUDE_DIR_CM24}
	  COMPONENT Development)
ENDIF(NOT XDMF_INSTALL_NO_DEVELOPMENT)

IF(NOT XDMF_INSTALL_NO_LIBRARIES)
  INSTALL(TARGETS Xdmf
    EXPORT ${XDMF_INSTALL_EXPORT_NAME}
    RUNTIME DESTINATION ${XDMF_INSTALL_BIN_DIR_CM24} COMPONENT Runtime
    LIBRARY DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24} COMPONENT Runtime
    ARCHIVE DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24} COMPONENT Development)
  IF(XDMF_WRAP_PYTHON)
    INSTALL(TARGETS _Xdmf
        EXPORT ${XDMF_INSTALL_EXPORT_NAME}
        RUNTIME DESTINATION ${XDMF_WRAP_PYTHON_INSTALL_DIR} COMPONENT Runtime
        LIBRARY DESTINATION ${XDMF_WRAP_PYTHON_INSTALL_DIR} COMPONENT Runtime)
  ENDIF(XDMF_WRAP_PYTHON)
ENDIF(NOT XDMF_INSTALL_NO_LIBRARIES)

OPTION(XDMF_BUILD_UTILS "Build Xdmf Utilities" OFF)
IF(XDMF_BUILD_UTILS)
   ADD_SUBDIRECTORY(utils)
ENDIF(XDMF_BUILD_UTILS)

IF(BUILD_TESTING)
  ADD_SUBDIRECTORY(test)
ENDIF(BUILD_TESTING)
