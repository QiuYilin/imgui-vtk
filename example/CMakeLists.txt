add_executable(example main.cpp imgui_vtk_demo.h)
target_link_libraries(example PRIVATE imgui::imgui glfw ${VTK_LIBRARIES} unofficial::gl3w::gl3w imgui_vtk)