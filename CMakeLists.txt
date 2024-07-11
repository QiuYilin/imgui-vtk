cmake_minimum_required(VERSION 3.6 FATAL_ERROR)
project(imgui-vtk)

set(CMAKE_CXX_STANDARD 11)
# set(CMAKE_VERBOSE_MAKEFILE 1)
set(APP_VERSION_MAJOR 1)
set(APP_VERSION_MINOR 0)
set(project_name imgui_vtk)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set(CMAKE_DEBUG_POSTFIX "d")
set(CMAKE_RELEASE_POSTFIX "")
set(CMAKE_RELWITHDEBINFO_POSTFIX "rd")
set(CMAKE_MINSIZEREL_POSTFIX "s")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/lib")


option(BUILD_EXAMPLE "BUILD_EXAMPLE" ON)

find_package(imgui CONFIG REQUIRED)
find_package(glfw3 CONFIG REQUIRED)
find_package(gl3w CONFIG REQUIRED)
find_package(VTK COMPONENTS 
  CommonCore
  CommonColor
  CommonDataModel
  FiltersCore
  InteractionStyle
  RenderingCore
  RenderingFreeType
  RenderingGL2PSOpenGL2
  RenderingOpenGL2
  QUIET
)

add_library(${project_name} STATIC include/VtkViewer.h src/VtkViewer.cpp)
target_link_libraries(${project_name} PRIVATE imgui::imgui glfw ${VTK_LIBRARIES} unofficial::gl3w::gl3w)

target_include_directories(${project_name}
    PUBLIC
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_PREFIX}/include/imgui-vtk>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

#if(BUILD_EXAMPLE)
  add_subdirectory(example)
#endif()

if (VTK_VERSION VERSION_LESS "9.0.0")
  include(${VTK_USE_FILE})
else()
  # vtk_module_autoinit is needed
  vtk_module_autoinit(
    TARGETS ${project_name}
    MODULES ${VTK_LIBRARIES}
  )
endif()


