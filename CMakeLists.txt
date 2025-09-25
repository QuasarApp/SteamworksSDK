cmake_minimum_required(VERSION 3.19)

# Set the project name
project(SteamworksSDK)

add_library(${PROJECT_NAME} SHARED IMPORTED)

if(WIN32)
    set(LIB_FILE "steam_api64.dll")
    set(LIB_URL "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/win64/${LIB_FILE}")

    # On Windows, link with the provided static library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_IMPLIB "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/win64/steam_api64.lib"
        IMPORTED_LOCATION "${LIB_URL}"
    )

elseif(APPLE)
    set(LIB_FILE "libsteam_api.dylib")
    set(LIB_URL "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/osx/${LIB_FILE}")

    # On macOS, link with the provided dynamic library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_LOCATION "${LIB_URL}"
    )
elseif(UNIX)
    set(LIB_FILE "libsteam_api.so")
    set(LIB_URL "${CMAKE_CURRENT_SOURCE_DIR}/redistributable_bin/linux64/${LIB_FILE}")

    # On Linux, link with the provided shared library
    set_target_properties(${PROJECT_NAME} PROPERTIES
        IMPORTED_LOCATION "${LIB_URL}"
    )

endif()

add_library(SteamworksSDK_Interface INTERFACE)
target_include_directories(SteamworksSDK_Interface INTERFACE "${CMAKE_CURRENT_SOURCE_DIR}/public")
target_link_libraries(SteamworksSDK_Interface INTERFACE SteamworksSDK)


ADD_CUSTOM_TARGET(
    SteamworksSDK_Interface_Bin
    COMMENT "${CMAKE_COMMAND} -E copy
    ${LIB_URL}
    ${CMAKE_CURRENT_BINARY_DIR}/${LIB_FILE}"

    COMMAND ${CMAKE_COMMAND} -E copy
    ${LIB_URL}
    ${CMAKE_CURRENT_BINARY_DIR}/steam_api/${LIB_FILE}
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}

)
add_dependencies(SteamworksSDK_Interface SteamworksSDK_Interface_Bin)

