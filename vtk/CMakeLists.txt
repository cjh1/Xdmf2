IF(NOT XDMF_INSTALL_INCLUDE_VTK_DIR)
    SET(XDMF_INSTALL_INCLUDE_VTK_DIR ${XDMF_INSTALL_INCLUDE_DIR})
ENDIF(NOT XDMF_INSTALL_INCLUDE_VTK_DIR)
IF(PV_INSTALL_BIN_DIR)
    SET(XDMF_INSTALL_BIN_DIR ${PV_INSTALL_BIN_DIR})
ENDIF(PV_INSTALL_BIN_DIR)
IF(PV_INSTALL_LIB_DIR)
    SET(XDMF_INSTALL_LIB_DIR ${PV_INSTALL_LIB_DIR})
ENDIF(PV_INSTALL_LIB_DIR)
IF(PV_INSTALL_INCLUDE_DIR)
    SET(XDMF_INSTALL_INCLUDE_DIR ${PV_INSTALL_INCLUDE_DIR})
ENDIF(PV_INSTALL_INCLUDE_DIR)

include(${ParaView_CMAKE_DIR}/VTKMono/vtkTargetExportMacros.cmake)
include(${ParaView_CMAKE_DIR}/VTKMono/vtkExportKit.cmake)

# This Needs to be done since Xdmf/CMakeLists.txt !=
# PV/Utils/Xdmf2/CMakeLists.txt
# Because INSTALL_* commands require a leading / and because INSTALL (cmake
# 2.4
# and newer) requires no leading / to install under INSTALL_PREFIX, we
# are stripping the leading /. In the future, there should be no leading
# / in any install directory variables
STRING(REGEX REPLACE "^/" "" XDMF_INSTALL_LIB_DIR_CM24 "${XDMF_INSTALL_LIB_DIR}")
STRING(REGEX REPLACE "^/" "" XDMF_INSTALL_BIN_DIR_CM24 "${XDMF_INSTALL_BIN_DIR}")
STRING(REGEX REPLACE "^/" "" XDMF_INSTALL_INCLUDE_DIR_CM24 "${XDMF_INSTALL_INCLUDE_DIR}")
STRING(REGEX REPLACE "^/" "" XDMF_INSTALL_INCLUDE_VTK_DIR_CM24 "${XDMF_INSTALL_INCLUDE_VTK_DIR}")



SET( Xdmf_SRCS
  vtkSILBuilder.cxx
  vtkXdmfDataArray.cxx
  vtkXdmfHeavyData.cxx
#vtkXdmfOpenGLCamera.cxx
  vtkXdmfReader.cxx
  vtkXdmfReaderInternal.cxx
  vtkXdmfWriter.cxx
#${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx
#vtkXdmfDataSetWriter.cxx
  )
SET_SOURCE_FILES_PROPERTIES(
  vtkXdmfReaderInternal
  vtkXdmfHeavyData
  WRAP_EXCLUDE
)


SET(USE_RENDER_WINDOW_INTERACTOR 1)
IF (OSMESA_LIBRARY AND NOT OPENGL_gl_LIBRARY)
  SET(USE_RENDER_WINDOW_INTERACTOR 0)
ENDIF (OSMESA_LIBRARY AND NOT OPENGL_gl_LIBRARY)

IF(APPLE)
  SET(USE_RENDER_WINDOW_INTERACTOR 0)
ENDIF(APPLE)

IF(USE_RENDER_WINDOW_INTERACTOR)
# Build vtkXdmf*RenderWindowInteractor.h
  IF(WIN32)
    SET(XDMF_WINDOWINTERACTOR vtkWin32RenderWindowInteractor)
    CONFIGURE_FILE(${Xdmf_SOURCE_DIR}/vtk/vtkXdmfRenderWindowInteractor.h.in
      ${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.h
      @ONLY IMMEDIATE)
    CONFIGURE_FILE(${Xdmf_SOURCE_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx.in
      ${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx
      @ONLY IMMEDIATE)
  ELSE(WIN32)
    IF(VTK_USE_X)
      INCLUDE_DIRECTORIES(${X11_X11_INCLUDE_PATH})
    ENDIF(VTK_USE_X)
    SET(XDMF_WINDOWINTERACTOR vtkXRenderWindowInteractor)
    CONFIGURE_FILE(${Xdmf_SOURCE_DIR}/vtk/vtkXdmfRenderWindowInteractor.h.in
      ${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.h
      @ONLY IMMEDIATE)
    CONFIGURE_FILE(${Xdmf_SOURCE_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx.in
      ${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx
      @ONLY IMMEDIATE)
  ENDIF(WIN32)

  SET(Xdmf_SRCS ${Xdmf_SRCS}
    ${Xdmf_BINARY_DIR}/vtk/vtkXdmfRenderWindowInteractor.cxx
    )
ENDIF(USE_RENDER_WINDOW_INTERACTOR)

SET_SOURCE_FILES_PROPERTIES(
  vtkXdmfDataArray
  WRAP_EXCLUDE
  )

SET(VTK_BINARY_DIR "${Xdmf_BINARY_DIR}/vtk")
INCLUDE(${ParaView_CMAKE_DIR}/VTKMono/vtkExportKit.cmake)
VTK_EXPORT_KIT("Xdmf" "XDMF" "${Xdmf_SRCS}")

SET(VTK_BINARY_DIR "${Xdmf_BINARY_DIR}/vtk")
# if vtk was found include the use vtk file
IF (VTK_FOUND)
  
  IF(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)
    SET(LIBTYPE SHARED)
  ELSE(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)
    SET(LIBTYPE STATIC)
  ENDIF(VTK_BUILD_SHARED_LIBS OR BUILD_SHARED_LIBS)

  PVVTK_ADD_LIBRARY(vtkXdmf ${LIBTYPE} ${Xdmf_SRCS})
  add_dependencies(vtkXdmf vtklibxml2)
  set(_xdmf_link_lib 
    vtkIOCore
    Xdmf
    vtkRenderingOpenGL
    vtkInteractionStyle
    vtkRenderingFreeTypeOpenGL
    )

  IF(VTK_USE_PARALLEL)
    list(APPEND _xdmf_link_lib vtkParallelCore)
  ENDIF(VTK_USE_PARALLEL)

  target_link_libraries(vtkXdmf ${_xdmf_link_lib})

  IF (VTK_USE_MPI)
    # Needed for mpich 2
    ADD_DEFINITIONS("-DMPICH_IGNORE_CXX_SEEK")
  ENDIF (VTK_USE_MPI)

IF(XDMF_BUILD_MPI)
    # Building MPI 
ELSE(XDMF_BUILD_MPI)
    ADD_DEFINITIONS("-DXDMF_NO_MPI")
ENDIF(XDMF_BUILD_MPI)


  # Create Tcl wrappers if VTK is wrapped in Tcl.
  IF(VTK_WRAP_TCL)
    INCLUDE(${VTK_CMAKE_DIR}/vtkWrapTcl.cmake OPTIONAL)
    VTK_WRAP_TCL2(vtk${PROJECT_NAME}TCL XdmfTCL_SRCS ${Xdmf_SRCS})
    PVVTK_ADD_LIBRARY(vtk${PROJECT_NAME}TCL ${LIBTYPE} ${XdmfTCL_SRCS} ${Xdmf_SRCS})
    IF(VTK_USE_PARALLEL)
      TARGET_LINK_LIBRARIES(vtk${PROJECT_NAME}TCL vtkIOTCL vtkRenderingTCL vtkParallelTCL Xdmf)
    ELSE(VTK_USE_PARALLEL)
      TARGET_LINK_LIBRARIES(vtk${PROJECT_NAME}TCL vtkIOTCL vtkRenderingTCL Xdmf)
    ENDIF(VTK_USE_PARALLEL)
    ADD_DEPENDENCIES(vtk${PROJECT_NAME}TCL vtk${PROJECT_NAME})
  ENDIF(VTK_WRAP_TCL)

  # Create Python wrappers if VTK is wrapped in Python.
  IF(VTK_WRAP_PYTHON)
    INCLUDE(${VTK_CMAKE_DIR}/vtkWrapPython.cmake OPTIONAL)
    VTK_WRAP_PYTHON2(vtk${PROJECT_NAME}Python XdmfPython_SRCS ${Xdmf_SRCS})
    PVVTK_ADD_LIBRARY(vtk${PROJECT_NAME}Python MODULE ${XdmfPython_SRCS} ${Xdmf_SRCS})
    set(_xdmf_link_lib 
      vtkIOCorePythonD 
      vtkRenderingCorePythonD
      Xdmf)
    
    if(VTK_USE_PARALLEL)
      list(APPEND _xdmf_link_lib vtkParallelCorePythonD) 
    endif()
    
    target_link_libraries(vtk${PROJECT_NAME}Python ${_xdmf_link_lib})
    
    ADD_DEPENDENCIES(vtk${PROJECT_NAME}Python vtk${PROJECT_NAME})
  ENDIF(VTK_WRAP_PYTHON)
ELSE(VTK_FOUND)
  MESSAGE(STATUS "Can't find VTK ... no vtkXdmf interface can be built")
ENDIF (VTK_FOUND)

IF(NOT XDMF_INSTALL_NO_DEVELOPMENT)
  FILE(GLOB dotHFiles 
      RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" 
      "*.h")
  FILE(GLOB binDotHFiles
      RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" 
     "${Xdmf_BINARY_DIR}/vtk/*.h")
  SET(devFiles "${dotHFiles};${binDotHFiles}")
  INSTALL(
      FILES ${devFiles}
      DESTINATION ${XDMF_INSTALL_INCLUDE_VTK_DIR_CM24}
      COMPONENT Development)
ENDIF(NOT XDMF_INSTALL_NO_DEVELOPMENT)
IF (VTK_FOUND)
  IF(NOT XDMF_INSTALL_NO_LIBRARIES)
      INSTALL(TARGETS vtkXdmf
	    EXPORT ${XDMF_INSTALL_EXPORT_NAME}
        RUNTIME DESTINATION ${XDMF_INSTALL_BIN_DIR_CM24} COMPONENT Runtime
        LIBRARY DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24} COMPONENT Runtime
        ARCHIVE DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24} COMPONENT Development)
    IF(VTK_WRAP_TCL)
      INSTALL(
          TARGETS vtk${PROJECT_NAME}TCL
          DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24})
#       INSTALL_TARGETS(${XDMF_INSTALL_LIB_DIR} vtk${PROJECT_NAME}TCL)
    ENDIF(VTK_WRAP_TCL)
    IF(VTK_WRAP_PYTHON)

      # Get the install path for python modules
      EXEC_PROGRAM("${PYTHON_EXECUTABLE}" "${Xdmf_SOURCE_DIR}/CMake/"
        ARGS "setup_install_paths.py" "purelib" --prefix=""
        OUTPUT_VARIABLE XDMF_WRAP_PYTHON_INSTALL_DIR)

      SET(XDMF_WRAP_PYTHON_INSTALL_DIR ${CMAKE_INSTALL_PREFIX}${XDMF_WRAP_PYTHON_INSTALL_DIR}/Xdmf)

      # Replace backslashes with forward slashes
      IF(WIN32)
      STRING(REGEX REPLACE "\\\\" "/" XDMF_WRAP_PYTHON_INSTALL_DIR
        "${XDMF_WRAP_PYTHON_INSTALL_DIR}")
      ENDIF(WIN32)

      IF(NOT PYTHON_INCLUDE_PATH)
        SET(PYTHON_INCLUDE_PATH ${XDMF_INSTALL_INCLUDE_VTK_DIR_CM24})
      ENDIF(NOT PYTHON_INCLUDE_PATH)

#      INSTALL(
#        TARGETS vtk${PROJECT_NAME}Python
#        DESTINATION ${XDMF_WRAP_PYTHON_INSTALL_DIR})
#       INSTALL(
#          TARGETS vtk${PROJECT_NAME}Python
#          DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24})
#        INSTALL_TARGETS(${XDMF_INSTALL_LIB_DIR} vtk${PROJECT_NAME}Python)

      INSTALL(
          TARGETS vtk${PROJECT_NAME}Python
          DESTINATION ${XDMF_INSTALL_LIB_DIR_CM24}
          COMPONENT Development)
    ENDIF(VTK_WRAP_PYTHON)
  ENDIF(NOT XDMF_INSTALL_NO_LIBRARIES)

  IF(BUILD_TESTING)
    ADD_SUBDIRECTORY(Testing)
  ENDIF(BUILD_TESTING)

ENDIF (VTK_FOUND)
