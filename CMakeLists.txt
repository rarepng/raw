cmake_minimum_required(VERSION 3.22)
project(raw LANGUAGES CXX)

#include(ExternalProject)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


find_package(Vulkan REQUIRED)
#find_package(Vulkan COMPONENTS glslc REQUIRED)
#find_package(Protobuf)
#find_package(GameNetworkingSockets)

set(submodules_path
        ${PROJECT_SOURCE_DIR}/sub
)

if(UNIX)
set(SHELL_CMD bash)
set(STATIC_LIB_EXT a)
else()
#set(SHELL_CMD pwsh)  #powershell 7
set(SHELL_CMD powershell) #default for windows 
set(STATIC_LIB_EXT lib)
endif()



#ExternalProject_Add(protoEX
#    SOURCE_DIR ${submodules_path}/protobuf
#    CMAKE_ARGS
#        ${GLOBAL_DEFAULT_ARGS}
#        -G Ninja
#        -DCMAKE_BUILD_TYPE=Release
#        -Dprotobuf_BUILD_TESTS=OFF
#        -Dprotobuf_BUILD_SHARED_LIBS=ON
#        -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto
#        -DCMAKE_CXX_STANDARD=20
#        -DCMAKE_CXX_STANDARD_REQUIRED=ON
#        -B ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto_build
#    BUILD_COMMAND ninja
#    INSTALL_COMMAND ninja install
#)
#
#set_property(GLOBAL PROPERTY Protobuf_LIBRARIES ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib)
#set_property(GLOBAL PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include)
#list(APPEND PATH ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin)
#ExternalProject_Add(gnsEX
#    SOURCE_DIR ${submodules_path}/gns
#    CMAKE_ARGS
#        ${GLOBAL_DEFAULT_ARGS}
#        -G Ninja
#        -DCMAKE_BUILD_TYPE=Release
#        -DProtobuf_LIBRARIES=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib
#        -DProtobuf_INCLUDE_DIR=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include
#        -B "${PROJECT_SOURCE_DIR}/cmake_more_cache/gns"
#    BUILD_COMMAND ninja
#)
#ExternalProject_Add_StepTargets()
#target_link_libraries(gnsTAR GLOBAL protobuf::libprotobuf)
#add_library(protobuf::libprotobuf SHARED IMPORTED)
#target_link_libraries(GameNetworkingSockets protobuf::libprotobuf)
#set_property(TARGET protobuf::libprotobuf PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin)
#set_property(TARGET protobuf::libprotobuf PROPERTY IMPORTED_IMPLIB ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib)
#ExternalProject_Add_StepDependencies(gnsEX configure protobuf::libprotobuf)
#ExternalProject_Add_Step(gnsEX prebuild
#    #COMMAND ${CMAKE_COMMAND} -E env PATH="$ENV{PATH};${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin"
#    #COMMAND ${CMAKE_COMMAND} set(PATH "$ENV{PATH};${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin")
#    COMMAND ${SHELL_CMD} -c "cd ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto"
#    COMMAND ${SHELL_CMD} -c "ninja install"
#    DEPENDERS configure
#)
#ExternalProject_Add_Step(gnsEX prebuild
#    #COMMAND ${CMAKE_COMMAND} -E env PATH="$ENV{PATH};${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin"
#    #COMMAND ${CMAKE_COMMAND} set(PATH "$ENV{PATH};${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin")
#    #COMMAND ${SHELL_CMD} -c "cd ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto"
#    #COMMAND ${SHELL_CMD} -c "ninja install"
#    DEPENDEES install
#)

#ExternalProject_Add_Step(gnsEX postbuild
#    #COMMAND ${CMAKE_COMMAND} -E env PATH="$ENV{PATH};${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin"
#    COMMAND ${SHELL_CMD} -c "cd ${PROJECT_SOURCE_DIR}/cmake_more_cache/gns/build"
#    COMMAND ${SHELL_CMD} -c "cp GameNetworkingSockets ${PROJECT_SOURCE_DIR}/lib"
#    DEPENDEES build
#)
#target_link_libraries(gnsEX PRIVATE protobuf::libprotobuf)
#target_link_directories(gnsEX "${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib")
#target_include_directories(gnsEX "${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include")

add_subdirectory(${submodules_path}/fastgltf)
add_subdirectory(${submodules_path}/glfw)
add_subdirectory(${submodules_path}/glm)
#add_subdirectory(${submodules_path}/openssl)
#add_subdirectory(${submodules_path}/protobuf)
#add_subdirectory(${submodules_path}/gns)
add_subdirectory(${submodules_path}/simdjson)
add_subdirectory(${submodules_path}/vk_boot)
add_subdirectory(${submodules_path}/vma)

file(GLOB PROJECT_SOURCES "src/*.cpp" "src/*/*.cpp")
set(OTHER_SOURCES
    ${submodules_path}/imgui/imgui.cpp
    ${submodules_path}/imgui/backends/imgui_impl_vulkan.cpp
    ${submodules_path}/imgui/backends/imgui_impl_glfw.cpp
    ${submodules_path}/imgui/misc/cpp/imgui_stdlib.cpp
    ${submodules_path}/imgui/imgui_tables.cpp
    ${submodules_path}/imgui/imgui_widgets.cpp
    ${submodules_path}/imgui/imgui_draw.cpp
    ${submodules_path}/imgui/imgui_demo.cpp
)

add_executable(raw ${OTHER_SOURCES} ${PROJECT_SOURCES})

file(GLOB shaders ${PROJECT_BINARY_DIR}/shaders/*)
list(LENGTH shaders shader_len)
#message(WARNING ${shader_len})
#set(Protobuf_LIBRARIES ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib)
#set(Protobuf_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include)

if(shader_len EQUAL 0)
file(GLOB_RECURSE GLSL_SOURCE_FILES
    "${PROJECT_SOURCE_DIR}/shaders/*.frag"
    "${PROJECT_SOURCE_DIR}/shaders/*.vert"
    )
  foreach(GLSL ${GLSL_SOURCE_FILES})
    file(TO_CMAKE_PATH "${GLSL}" GLSL_NATIVE)
    cmake_path(GET GLSL_NATIVE FILENAME SPIRV_NAME)
      set(SPIRV "${PROJECT_BINARY_DIR}/shaders/${SPIRV_NAME}.spv")
      add_custom_command(
        OUTPUT ${SPIRV}
        COMMAND ${CMAKE_COMMAND} -E make_directory "${PROJECT_BINARY_DIR}/shaders"
        COMMAND ${SHELL_CMD} -c "$ENV{VULKAN_SDK}/bin/glslc --target-env=vulkan1.3 --target-spv=spv1.6 -std=460 -c ${GLSL_NATIVE} -o ${SPIRV}"
        DEPENDS ${GLSL_NATIVE}
      )
      list(APPEND SPIRV_BINARY_FILES ${SPIRV})
  endforeach()
    set_source_files_properties(${SPIRV_BINARY_FILES} PROPERTIES GENERATED TRUE)
  add_custom_target(shaders ALL DEPENDS ${SPIRV_BINARY_FILES})
  add_dependencies(raw shaders)
endif()




set(resources_bin_dir "${PROJECT_BINARY_DIR}/resources")
add_custom_command(
    OUTPUT ${resources_bin_dir}
    COMMAND ${CMAKE_COMMAND} -E copy_directory "${PROJECT_SOURCE_DIR}/resources" "${PROJECT_BINARY_DIR}/resources"
    DEPENDS "${PROJECT_SOURCE_DIR}/resources"
)
add_custom_target(resources_bin_deps DEPENDS ${PROJECT_BINARY_DIR}/resources)
add_dependencies(raw resources_bin_deps)

#target_link_directories(raw PUBLIC ${PROJECT_SOURCE_DIR}/lib ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin)
target_link_directories(raw PUBLIC ${PROJECT_SOURCE_DIR}/lib)

#add_dependencies(raw gnsEX)

target_link_libraries(raw
    PUBLIC
        glfw
        fastgltf
        glm
        Vulkan::Vulkan
        vk-bootstrap::vk-bootstrap
        GameNetworkingSockets
)

target_include_directories(raw
    PUBLIC
        #"${VULKAN_SDK}/Include"
        "${submodules_path}/gns/include"
        "${submodules_path}/stb"
        "${submodules_path}/imgui"
        "${submodules_path}/vma/include"
        "${PROJECT_SOURCE_DIR}/src"
        #${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include
)
#target_include_directories(${submodules_path}/gns
#PUBLIC
#${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include)


set_property(TARGET raw APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${PROJECT_BINARY_DIR}/resources)
set_property(TARGET raw APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${PROJECT_BINARY_DIR}/shaders)
