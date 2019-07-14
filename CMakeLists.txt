cmake_minimum_required(VERSION 3.5)
project(VulkanMemoryAllocator VERSION 1.0.0 LANGUAGES CXX)

find_package(Vulkan REQUIRED)

file(GENERATE OUTPUT "${CMAKE_BINARY_DIR}/vk_mem_alloc.cpp"
    CONTENT "#define VMA_IMPLEMENTATION\n#include \"vk_mem_alloc.h\"\n"
)

add_library(VulkanMemoryAllocator "${CMAKE_BINARY_DIR}/vk_mem_alloc.cpp")

add_library(VulkanMemoryAllocator::VulkanMemoryAllocator ALIAS VulkanMemoryAllocator)

target_include_directories(VulkanMemoryAllocator
    PUBLIC
        $<INSTALL_INTERFACE:include>
        
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_link_libraries(VulkanMemoryAllocator PUBLIC Vulkan::Vulkan)

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/VulkanMemoryAllocator)

install(TARGETS VulkanMemoryAllocator
    EXPORT VulkanMemoryAllocator-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(FILES 
    src/vk_mem_alloc.h
DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/VulkanMemoryAllocator)

install(EXPORT VulkanMemoryAllocator-targets
  FILE
    VulkanMemoryAllocatorTargets.cmake
  NAMESPACE
    VulkanMemoryAllocator::
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/VulkanMemoryAllocatorConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/VulkanMemoryAllocatorConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/VulkanMemoryAllocatorConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/VulkanMemoryAllocatorConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/VulkanMemoryAllocatorConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

export(EXPORT VulkanMemoryAllocator-targets FILE ${CMAKE_CURRENT_BINARY_DIR}/VulkanMemoryAllocatorTargets.cmake NAMESPACE VulkanMemoryAllocator::)

export(PACKAGE VulkanMemoryAllocator)