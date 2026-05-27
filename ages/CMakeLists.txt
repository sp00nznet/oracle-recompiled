cmake_minimum_required(VERSION 3.16)
project(oracle_of_ages C CXX)

# Set C standard
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Aggressive optimization flags
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    add_compile_options(-O3)
endif()

# Runtime library path (relative to this output directory)
set(GBRT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../gbrecomp/runtime")

# Find SDL2
find_package(SDL2 REQUIRED)

# Create runtime library with PPU and platform support
add_library(gbrt STATIC
    ${GBRT_DIR}/src/gbrt.c
    ${GBRT_DIR}/src/ppu.c
    ${GBRT_DIR}/src/audio.c
    ${GBRT_DIR}/src/interpreter.c
    ${GBRT_DIR}/src/platform_sdl.cpp
)

# ImGui sources
target_sources(gbrt PRIVATE
    ${GBRT_DIR}/third_party/imgui/imgui.cpp
    ${GBRT_DIR}/third_party/imgui/imgui_draw.cpp
    ${GBRT_DIR}/third_party/imgui/imgui_tables.cpp
    ${GBRT_DIR}/third_party/imgui/imgui_widgets.cpp
    ${GBRT_DIR}/third_party/imgui/imgui_demo.cpp
    ${GBRT_DIR}/third_party/imgui/backends/imgui_impl_sdl2.cpp
    ${GBRT_DIR}/third_party/imgui/backends/imgui_impl_sdlrenderer2.cpp
)
target_include_directories(gbrt PUBLIC 
    ${GBRT_DIR}/include
    ${GBRT_DIR}/third_party/imgui
)
target_link_libraries(gbrt PUBLIC SDL2::SDL2)
target_compile_definitions(gbrt PUBLIC GB_HAS_SDL2)

# Main executable
add_executable(oracle_of_ages
    oracle_of_ages_main.c
    oracle_of_ages.c
    oracle_of_ages_rom.c
)

target_link_libraries(oracle_of_ages gbrt)
