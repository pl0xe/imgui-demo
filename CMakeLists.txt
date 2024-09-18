CMAKE_MINIMUM_REQUIRED(VERSION 3.20)

project(imgui-demo VERSION 1.0
                DESCRIPTION "an example setup of imgui"
                LANGUAGES CXX C)

set(IMGUI_SOURCES
    libs/imgui/imgui.cpp
    libs/imgui/imgui_demo.cpp
    libs/imgui/imgui_draw.cpp
    libs/imgui/imgui_tables.cpp
    libs/imgui/imgui_widgets.cpp)

set(IMGUI_BACKEND
    libs/imgui/backends/imgui_impl_opengl3.cpp
    libs/imgui/backends/imgui_impl_sdl2.cpp)

set(SOURCES
    src/main.cpp
    ${IMGUI_SOURCES}
    ${IMGUI_BACKEND})

set(SDL_SHARED off)
set(SDL_STATIC on)

find_package(OpenGL)

add_subdirectory(libs/SDL)

add_executable(app ${SOURCES})

target_include_directories(app PRIVATE libs/imgui libs/imgui/backends libs/SDL/include)

target_link_libraries(app PRIVATE ${OPENGL_LIBRARIES} SDL2-static SDL2-static)