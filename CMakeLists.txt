cmake_minimum_required(VERSION 3.30)
project(raw LANGUAGES C CXX)


include(ExternalProject)



set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


find_package(Vulkan REQUIRED)
#find_package(Vulkan COMPONENTS glslc REQUIRED)
#find_package(absl REQUIRED)
#find_package(protobuf REQUIRED)
#find_package(GameNetworkingSockets REQUIRED)

set(OPENSSL_USE_STATIC_LIBS ON)
find_package(OpenSSL REQUIRED)

#find_package(Protobuf REQUIRED)

set(submodules_path
        ${PROJECT_SOURCE_DIR}/sub
)

if(UNIX)
set(SHELL_CMD bash)
set(STATIC_LIB_EXT a)
set(SEP ":")
else()
#set(SHELL_CMD pwsh)  #powershell 7
set(SHELL_CMD powershell) #default for windows 
set(STATIC_LIB_EXT lib)
set(SEP ";")
endif()



add_definitions(-DSTEAMNETWORKINGSOCKETS_STATIC_LINK=1)
add_definitions(-DABSL_OPTION_USE_STD_ORDERING=0)
        


        #-DCMAKE_CXX_FLAGS='-static\ -I${PROJECT_SOURCE_DIR}/cmake_more_cache/absl/include'
        #-Dprotobuf_ABSL_PROVIDER=package
        #-DCMAKE_PREFIX_PATH=${PROJECT_SOURCE_DIR}/cmake_more_cache/absl/lib/cmake/absl
        #-DABSL_ROOT_DIR=${PROJECT_SOURCE_DIR}/cmake_more_cache/absl/lib/cmake/absl




        #-DProtobuf_LIBRARIES=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib/cmake/protobuf
        #-DProtobuf_INCLUDE_DIR=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include
        #-DProtobuf_PROTOC_EXECUTABLE=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/bin/protoc
        #-Dprotobuf_DIR=${PROJECT_SOURCE_DIR}/cmake_more_cache/proto



        #-DCMAKE_CXX_FLAGS='--static\ -static\ -I${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/include\ -I${PROJECT_SOURCE_DIR}/cmake_more_cache/absl/include\ -L${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib\ -L${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/absl\ -lprotobuf\ -lprotoc\ -lutf8_range'


set(BUILD_TESTING OFF CACHE BOOL "set" FORCE)
set(CMAKE_BUILD_TYPE Release CACHE STRING "set" FORCE)

set(BUILD_SHARED_LIB OFF CACHE BOOL "set" FORCE)
set(BUILD_SHARED_LIBS OFF CACHE BOOL "set" FORCE)
set(BUILD_STATIC_LIBS ON CACHE BOOL "set" FORCE)
set(BUILD_STATIC_LIB ON CACHE BOOL "set" FORCE)

set(protobuf_BUILD_TESTS OFF CACHE BOOL "set" FORCE)
set(protobuf_BUILD_SHARED_LIBS OFF CACHE BOOL "set" FORCE)
set(Protobuf_USE_STATIC_LIBS ON CACHE BOOL "set" FORCE)
set(OPENSSL_USE_STATIC_LIBS ON CACHE BOOL "set" FORCE)
set(ABSL_ENABLE_INSTALL ON CACHE BOOL "set" FORCE)

set(ABSL_OPTION_USE_STD_ORDERING 0 CACHE BOOL "set" FORCE)
set(ABSL_PROPAGATE_CXX_STD ON CACHE BOOL "set" FORCE)

set(STEAMNETWORKINGSOCKETS_STATIC_LINK 1 CACHE BOOL "set" FORCE)


set(CMAKE_CXX_COMPILER g++ CACHE STRING "set" FORCE)
set(CMAKE_C_COMPILER gcc CACHE STRING "set" FORCE)

set(CMAKE_CXX_STANDARD 20 CACHE STRING "set" FORCE)
set(CMAKE_CXX_STANDARD_REQUIRED ON CACHE BOOL "set" FORCE)

set(CMAKE_BUILD_TYPE Release CACHE STRING "set" FORCE)

set(CMAKE_CXX_FLAGS='--static\ -static\ -D\ ABSL_OPTION_USE_STD_ORDERING=0\ -D\ ABSL_USES_STD_ORDERING=0\ -D\ STEAMNETWORKINGSOCKETS_STATIC_LINK=1')



set (CMAKE_GENERATOR "Ninja" CACHE INTERNAL "" FORCE)

set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/cmake_more_cache/installs" CACHE PATH "installs" FORCE) #USELESSS!!!!!!!!!!!


add_subdirectory(${submodules_path}/absl ${PROJECT_SOURCE_DIR}/cmake_more_cache/absl)#ABSL_OPTION_USE_STD_ORDERING=0












add_subdirectory(${submodules_path}/protobuf ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto)#protobuf_ABSL_PROVIDER=package ABSL_ROOT_DIR=${submodules_path}/absl





##target_link_libraries(libprotobuf PRIVATE absl::absl_check)
##target_link_libraries(libprotobuf-lite PRIVATE absl_check)
##target_link_libraries(libprotoc PRIVATE absl_check)
#install(TARGETS libprotobuf DESTINATION ${PROJECT_SOURCE_DIR}/protobuf)

#add_custom_target(build_proto
#    COMMAND ${CMAKE_COMMAND} --build ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto
#    DEPENDS libprotobuf
#)
#
add_custom_target(install_proto
    COMMAND ${CMAKE_COMMAND} -P instproto.cmake
    DEPENDS libprotobuf
)
add_custom_target(link_gns
    COMMAND ${CMAKE_COMMAND} -P addgns.cmake
    DEPENDS install_proto
)

#install(TARGETS libprotobuf protoc libprotobuf-lite libprotoc libupb)



set(Protobuf_LIBRARIES "${PROJECT_SOURCE_DIR}/cmake_more_cache/installs/lib" CACHE PATH "set" FORCE)
set(Protobuf_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/cmake_more_cache/installs/include" CACHE PATH "set" FORCE)
set(Protobuf_PROTOC_EXECUTABLE "${PROJECT_SOURCE_DIR}/cmake_more_cache/installs/bin/protoc" CACHE FILEPATH "set" FORCE)


#list(APPEND CMAKE_PREFIX_PATH  "${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/cmake/protobuf" FORCE)
list(APPEND CMAKE_PREFIX_PATH  "${PROJECT_SOURCE_DIR}/cmake_more_cache/installs/bin" FORCE)
#add_custom_command(
#    TARGET protoc
#    POST_BUILD
#    COMMAND ${CMAKE_COMMAND} --install ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto 
#    COMMAND ${CMAKE_COMMAND} -P addgns.cmake
#    VERBATIM
#    POST_INSTALL_SCRIPT
#    #WORKING_DIRECTORY 
#)




#set(Protobuf_DIR ${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/cmake/protobuf)
#add_subdirectory(${submodules_path}/gns ${PROJECT_SOURCE_DIR}/cmake_more_cache/gns)






#target_compile_definitions(gnsEX PUBLIC STEAMNETWORKINGSOCKETS_STATIC_LINK)

add_subdirectory(${submodules_path}/simdjson)
add_subdirectory(${submodules_path}/fastgltf)
add_subdirectory(${submodules_path}/glfw)
add_subdirectory(${submodules_path}/glm)
add_subdirectory(${submodules_path}/vk_boot)
add_subdirectory(${submodules_path}/vma)



set(Protobuf_LIBRARIES ${PROJECT_SOURCE_DIR}/cmake_more_cache/protobuf/lib/cmake/protobuf CACHE STRING "set" FORCE)
set(Protobuf_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/cmake_more_cache/protobuf/include CACHE STRING "set" FORCE)
set(Protobuf_PROTOC_EXECUTABLE ${PROJECT_SOURCE_DIR}/cmake_more_cache/protobuf/protoc CACHE STRING "set" FORCE)
#set(protobuf_DIR ${PROJECT_SOURCE_DIR}/cmake_more_cache/protobuf CACHE STRING "set" FORCE)
#set(Protobuf_ROOT ${PROJECT_SOURCE_DIR}/cmake_more_cache/protobuf CACHE STRING "set" FORCE)




#add_subdirectory(${submodules_path}/gns ${PROJECT_SOURCE_DIR}/cmake_more_cache/gns)#STEAMNETWORKINGSOCKETS_STATIC_LINK=1


#add_dependencies(GameNetworkingSockets_s protoEX)

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


#set_target_properties(raw PROPERTIES CMAKE_CXX_FLAGS '-static\ -l${PROJECT_SOURCE_DIR}/cmake_more_cache/gns/lib/GameNetworkingSockets_s')


file(GLOB shaders ${PROJECT_BINARY_DIR}/shaders/*)
list(LENGTH shaders shader_len)

#if(shader_len EQUAL 0)
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
#endif()




set(resources_bin_dir "${PROJECT_BINARY_DIR}/resources")
add_custom_command(
    OUTPUT ${resources_bin_dir}
    COMMAND ${CMAKE_COMMAND} -E copy_directory "${PROJECT_SOURCE_DIR}/resources" "${PROJECT_BINARY_DIR}/resources"
    DEPENDS "${PROJECT_SOURCE_DIR}/resources"
)
add_custom_target(resources_bin_deps DEPENDS ${PROJECT_BINARY_DIR}/resources)
add_dependencies(raw resources_bin_deps)

add_dependencies(raw link_gns)

#target_link_directories(raw PRIVATE
#${PROJECT_SOURCE_DIR}/cmake_more_cache/gns/lib
#${PROJECT_SOURCE_DIR}/cmake_more_cache/proto/lib
#
#)

target_include_directories(raw PUBLIC ${PROJECT_SOURCE_DIR}/sub/gns/include)



#add_library(absl::strings ALIAS absl_strings)

#include(addgns.cmake)

target_link_libraries(raw
    PRIVATE
        glfw
        fastgltf
        glm
        Vulkan::Vulkan
        vk-bootstrap::vk-bootstrap
        GameNetworkingSockets_s
        #protobuf
        #absl::any
        #GameNetworkingSockets
        #${PROJECT_SOURCE_DIR}/cmake_more_cache/gns/lib/cmake/GameNetworkingSockets
        #utf8_range
        #OpenSSL::SSL
        #OpenSSL::Crypto
        #protobuf
        #absl_synchronization
        #absl_symbolize
        #absl_strings              
        #absl_str_format_internal    
        #absl_strings_internal         
        #absl_log_initialize           
        #absl_log_entry                
        #absl_log_flags                
        #absl_log_severity             
        #absl_log_internal_check_op    
        #absl_log_internal_conditions  
        #absl_log_internal_message     
        #absl_log_internal_nullguard   
        #absl_log_internal_proto       
        #absl_log_internal_format      
        #absl_log_internal_globals     
        #absl_log_internal_log_sink_set
        #absl_log_sink                 
        #absl_raw_logging_internal     
        #absl_log_globals              
        ##utf8_validity                 
        #absl_cord                     
        #absl_cordz_info               
        #absl_cordz_handle             
        #absl_cordz_functions          
        #absl_cord_internal            
        #absl_crc_cord_state           
        #absl_crc32c                   
        #absl_crc_internal             
        #absl_exponential_biased       
        #absl_synchronization          
        #absl_time                     
        #absl_time_zone                
        #absl_int128                   
        #absl_examine_stack            
        #absl_stacktrace               
        #absl_symbolize                
        #absl_demangle_internal        
        #absl_debugging_internal       
        #absl_malloc_internal          
        #absl_throw_delegate           
        #absl_strerror                 
        #absl_raw_hash_set             
        #absl_hash                     
        #absl_city                     
        #absl_low_level_hash           
        #absl_base                     
        #absl_spinlock_wait
        #absl_status
        ##abls_synchronization_internal

)

target_include_directories(raw
    PUBLIC
        "${PROJECT_SOURCE_DIR}/cmake_more_cache/gns/include/GameNetworkingSockets"
        #"${submodules_path}/gns/include"
        "${submodules_path}/stb"
        "${submodules_path}/imgui"
        "${submodules_path}/vma/include"
        "${PROJECT_SOURCE_DIR}/src"
)



set_property(TARGET raw APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${PROJECT_BINARY_DIR}/resources)
set_property(TARGET raw APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${PROJECT_BINARY_DIR}/shaders)


