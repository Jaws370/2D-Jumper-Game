# basic cmake file stuff
cmake_minimum_required(VERSION 3.16)
project(2D-Jumper-Game LANGUAGES CXX)

# set output for the exe and turn off shared libraries
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/exe)
# shared libraries are not compiled with exe whereas static are
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# add libraries add_subdirectory(src/board) add_subdirectory(src/piece_types)
# add_subdirectory(src/game_data_controller)

# include sfml packages
include(FetchContent)
FetchContent_Declare(
  SFML
  GIT_REPOSITORY https://github.com/SFML/SFML.git
  GIT_TAG 2.6.x)
FetchContent_MakeAvailable(SFML)

# add exe
add_executable(2D-Jumper-Game src/main.cpp)

# link our libraries to exe target_link_libraries(2D-Jumper-Game PUBLIC board)
# target_link_libraries(2D-Jumper-Game PUBLIC piece_types)
# target_link_libraries(2D-Jumper-Game PUBLIC game_data_controller)

# link sfml to exe
target_link_libraries(2D-Jumper-Game PRIVATE sfml-graphics)

# include hpp file directories for our libraries target_include_directories(
# 2D-Jumper-Game PUBLIC "${PROJECT_BINARY_DIR}/src/board"
# "${PROJECT_BINARY_DIR}/src/game_data_controller")

# sets the compiler to C++17 (makes it so that if used, it can use newer syntax)
target_compile_features(2D-Jumper-Game PRIVATE cxx_std_17)

# adds openal32.dll for windows machines
if(WIN32)
  add_custom_command(
    TARGET 2D-Jumper-Game
    COMMENT "Copy OpenAL DLL"
    PRE_BUILD
    COMMAND
      ${CMAKE_COMMAND} -E copy
      ${SFML_SOURCE_DIR}/extlibs/bin/$<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>/openal32.dll
      $<TARGET_FILE_DIR:2D-Jumper-Game>
    VERBATIM)
endif()

# copies assets to exe
add_custom_command(
  TARGET 2D-Jumper-Game
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/assets
          $<TARGET_FILE_DIR:2D-Jumper-Game>/assets)

# installs packages & binary files corresponding to targets
install(TARGETS 2D-Jumper-Game)
