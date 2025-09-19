cmake_minimum_required(VERSION 3.19)

# Set the project name
project(SteamworksSDK)

add_library(${PROJECT_NAME} SHARED IMPORTED)

if(WIN32)
    # On Windows, link with the provided static library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_IMPLIB "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/win64/steam_api64.lib"
        IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/win64/steam_api64.dll"
    )

elseif(APPLE)
    # On macOS, link with the provided dynamic library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/osx/libsteam_api.dylib"
    )
elseif(UNIX)
    # On Linux, link with the provided shared library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/linux64/libsteam_api.so"
    )
endif()



add_library(SteamworksSDK_Interface INTERFACE)
target_include_directories(SteamworksSDK_Interface INTERFACE "${CMAKE_CURRENT_SOURCE_DIR}/public")
target_link_libraries(SteamworksSDK_Interface INTERFACE SteamworksSDK)
