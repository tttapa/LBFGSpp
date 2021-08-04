cmake_minimum_required(VERSION 3.16)

project(LBFGSpp VERSION 0.0.1 LANGUAGES CXX)
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# Dependencies
find_package(Eigen3 REQUIRED)

# LBFGSpp library
add_library(LBFGSpp INTERFACE)
add_library(LBFGSpp::LBFGSpp ALIAS LBFGSpp)
target_link_libraries(LBFGSpp INTERFACE Eigen3::Eigen)
target_include_directories(LBFGSpp
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# LBFGSpp library installation
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/LBFGSpp)
install(
    TARGETS
        LBFGSpp
    EXPORT
        LBFGSppTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(EXPORT LBFGSppTargets
    FILE LBFGSppTargets.cmake
    NAMESPACE LBFGSpp::
    DESTINATION ${INSTALL_CONFIGDIR}
)

install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/LBFGSppConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    ${CMAKE_CURRENT_LIST_DIR}/cmake/LBFGSppConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/LBFGSppConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/LBFGSppConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/LBFGSppConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

# LBFGSpp examples
set(LBFGSpp_EXAMPLE_NAMES
    "example-quadratic"
    "example-rosenbrock-box"
    "example-rosenbrock-bracketing"
    "example-rosenbrock-comparison"
    "example-rosenbrock")
foreach(EXAMPLE_NAME ${LBFGSpp_EXAMPLE_NAMES})
    add_executable(${EXAMPLE_NAME} EXCLUDE_FROM_ALL ${EXAMPLE_NAME}.cpp)
    target_link_libraries(${EXAMPLE_NAME} LBFGSpp::LBFGSpp)
endforeach()
add_custom_target(examples DEPENDS ${LBFGSpp_EXAMPLE_NAMES})
