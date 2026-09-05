add_library(ServerUtilities INTERFACE)
add_library(ServerUtilities::ServerUtilities ALIAS ServerUtilities)

target_sources(
    ServerUtilities
    INTERFACE
    FILE_SET HEADERS TYPE HEADERS
    BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include
    FILES ${CMAKE_CURRENT_SOURCE_DIR}/include/serverutilities/serverutilities.h
)

find_package(boost_beast CONFIG REQUIRED)
find_package(boost_charconv CONFIG REQUIRED)
find_package(boost_json CONFIG REQUIRED)

if(NOT TARGET ServerError::ServerError)
    find_package(ServerError CONFIG QUIET)

    if(NOT ServerError_FOUND)
        include(FetchContent)
        FetchContent_Declare(
            ServerError
            GIT_REPOSITORY https://github.com/Bernard-wuam/servererror.git
            GIT_TAG main
        )
        FetchContent_MakeAvailable(ServerError)
    endif()
endif()

target_link_libraries(ServerUtilities INTERFACE Boost::json Boost::beast ServerError::ServerError)
install(
    TARGETS ServerUtilities
    EXPORT ServerUtilitiesTargets
    FILE_SET HEADERS
)

install(
    EXPORT ServerUtilitiesTargets
    DESTINATION share/cmake/serverutilities
    NAMESPACE ServerUtilities::
)

include(CMakePackageConfigHelpers)

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/serverutilitiesConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/ServerUtilitiesConfig.cmake
    INSTALL_DESTINATION share/cmake/serverutilities)

write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/ServerUtilitiesConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)

install(
    FILES
    ${CMAKE_CURRENT_BINARY_DIR}/ServerUtilitiesConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/ServerUtilitiesConfigVersion.cmake
    DESTINATION share/cmake/serverutilities
)
