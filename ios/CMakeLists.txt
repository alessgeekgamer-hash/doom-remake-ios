cmake_minimum_required(VERSION 3.21)
project(DoomRemake C)

# SDL3 sources: read-only clone placed at SDL/ (repo root) by the CI workflow
add_subdirectory(
    ${CMAKE_CURRENT_SOURCE_DIR}/../SDL
    ${CMAKE_CURRENT_BINARY_DIR}/SDL
)

set(DOOM_EXAMPLE ${CMAKE_CURRENT_SOURCE_DIR}/../examples/SDL/sdl_example.c)
set(DOOM_WAD ${CMAKE_CURRENT_SOURCE_DIR}/../doom1.wad)
set(DOOM_STORYBOARD ${CMAKE_CURRENT_SOURCE_DIR}/LaunchScreen.storyboard)

add_executable(DoomRemake MACOSX_BUNDLE ${DOOM_EXAMPLE})

set_target_properties(DoomRemake PROPERTIES
    OUTPUT_NAME "DOOM Remake"
    MACOSX_BUNDLE TRUE
    MACOSX_BUNDLE_BUNDLE_NAME "DOOM Remake"
    MACOSX_BUNDLE_GUI_IDENTIFIER "com.doomremake.game"
    MACOSX_BUNDLE_BUNDLE_VERSION "1.0"
    MACOSX_BUNDLE_SHORT_VERSION_STRING "1.0"
    MACOSX_BUNDLE_INFO_PLIST ${CMAKE_CURRENT_SOURCE_DIR}/Info.plist
)

target_compile_definitions(DoomRemake PRIVATE
    DOOM_IOS
    DOOM_EXAMPLE_USE_SINGLE_HEADER
    DOOM_IMPLEMENT_PRINT
    DOOM_IMPLEMENT_MALLOC
    DOOM_IMPLEMENT_FILE_IO
    DOOM_IMPLEMENT_SOCKETS
    DOOM_IMPLEMENT_GETTIME
    DOOM_IMPLEMENT_EXIT
    DOOM_IMPLEMENT_GETENV
)

target_link_libraries(DoomRemake PRIVATE SDL3::SDL3)

# Bundle resources: the WAD, the launch screen and the home screen icons
file(GLOB IOS_ICONS ${CMAKE_CURRENT_SOURCE_DIR}/icons/*.png)
foreach(icon ${IOS_ICONS})
    set_source_files_properties(${icon} PROPERTIES MACOSX_PACKAGE_LOCATION Resources)
endforeach()
set_source_files_properties(${DOOM_WAD} PROPERTIES MACOSX_PACKAGE_LOCATION Resources)
set_source_files_properties(${DOOM_STORYBOARD} PROPERTIES MACOSX_PACKAGE_LOCATION Resources)
target_sources(DoomRemake PRIVATE ${DOOM_WAD} ${DOOM_STORYBOARD} ${IOS_ICONS})
