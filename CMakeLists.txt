cmake_minimum_required(VERSION 3.10)
project(barony)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# --- DEFINICIÓN DE MACROS PARA CONFIG.HPP (Evita Error C1017) ---
set(STEAMWORKS_ENABLED 0)
set(EOS_ENABLED 1)
set(PLAYFAB_ENABLED 0)
set(THEORAPLAYER_ENABLED 0)
set(CURL_ENABLED 0)
set(OPUS_ENABLED 0)
set(EDITOR_ENABLED 0)
set(GAME_ENABLED 1)

# IDs de Epic Games Store
set(BUILD_ENV_PR "38276f7f6f4d4127929424c803027b40" CACHE STRING "Product ID")
set(BUILD_ENV_SA "899732f74136450992383842c1630132" CACHE STRING "Sandbox ID")
set(BUILD_ENV_DE "39f3796d19ca447285ec43764835848e" CACHE STRING "Deployment ID")
set(BUILD_ENV_CC "xyza78912q6mHnd5O1TjL2H9YmD83W4v" CACHE STRING "Client ID")
set(BUILD_ENV_CS "1" CACHE STRING "Bypass")
set(BUILD_ENV_GSE "1" CACHE STRING "Bypass")

# Flags Globales
set(USE_EOS 1)
set(EGP 1)
set(Windows 1)
add_definitions(-DWIN64 -D_CRT_SECURE_NO_WARNINGS -DNOMINMAX -DEGP -DUSE_EOS -D__STDC_FORMAT_MACROS)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")

# Dependencias (vcpkg)
find_package(OpenGL REQUIRED)
find_package(SDL2 REQUIRED)
find_package(SDL2_image REQUIRED)
find_package(SDL2_net REQUIRED)
find_package(SDL2_ttf REQUIRED)
find_package(PhysFS REQUIRED)
find_package(RapidJson REQUIRED)
find_package(OpenAL REQUIRED)
find_package(OGGVORBIS REQUIRED)
find_package(PNG REQUIRED)
find_package(Threads REQUIRED)
find_package(JSONCPP REQUIRED)

# Inclusión de Headers
include_directories("deps/eos/include")
include_directories("src")

# Generación de Config.hpp
configure_file("${PROJECT_SOURCE_DIR}/src/Config.hpp.in" "${PROJECT_SOURCE_DIR}/src/Config.hpp")

add_subdirectory(src)

# Ejecutable
add_executable(barony ${GAME_SOURCES} "${CMAKE_CURRENT_SOURCE_DIR}/barony.rc")

target_link_libraries(barony 
    ${SDL2_LIBRARIES} 
    ${SDL2_IMAGE_LIBRARIES} 
    ${SDL2_NET_LIBRARIES} 
    ${SDL2_TTF_LIBRARIES} 
    ${OPENAL_LIBRARY} 
    ${VORBISFILE_LIBRARY} 
    ${OGG_LIBRARY} 
    ${PHYSFS_LIBRARY} 
    ${PNG_LIBRARY} 
    ${OPENGL_LIBRARIES} 
    ${JSONCPP_LIBRARIES}
    ${THREADS_LIBRARIES}
    ws2_32 wsock32 Advapi32 User32 Gdi32 Shell32 Ole32 Crypt32
)

# Linkeo del SDK de Epic
target_link_libraries(barony "${CMAKE_SOURCE_DIR}/deps/eos/lib/EOSSDK-Win64-Shipping.lib")