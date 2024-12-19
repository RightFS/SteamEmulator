if(MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall)
endif()
find_package(Protobuf CONFIG REQUIRED)
#get_target_property(PROTOBUF_LIBRARIES protobuf::libprotobuf INTERFACE_LINK_LIBRARIES)
#message(STATUS "PROTOBUF_PROTOC_EXECUTABLE: ${PROTOBUF_LIBRARIES}")
#message(STATUS "Using Protocol Buffers: ${protobuf_VERSION}")
#set(PROTOBUF_PROTOC_EXECUTABLE protoc)
file(GLOB PROTO_FILES ${CMAKE_CURRENT_SOURCE_DIR}/*.proto)
protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS ${PROTO_FILES})
add_library(proto OBJECT ${PROTO_SRCS} ${PROTO_HDRS})
if (MSVC)
    set_property(TARGET proto PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /utf-8 /wd4100")
endif (MSVC)
# 这里设置为 PUBLIC 是因为在链接生成 exe 的时候, 需要这些 include
# include 的本质就是将那些东西复制进来, 所以 main.cpp 上面就会 include PROTOBUF_INCLUDE_DIR
# 因此需要设置为 PUBLIC 才行
#include_directories(${CMAKE_CURRENT_SOURCE_DIR})

target_include_directories(
        proto
        PUBLIC
#        ${Protobuf_INCLUDE_DIRS}
#        ${PROTO_HDRS}
        ${CMAKE_CURRENT_BINARY_DIR}
)
target_link_libraries(proto PUBLIC protobuf::libprotobuf)
#target_link_libraries(${PROJECT_NAME} PRIVATE ${PROTOBUF_STATIC_LIBRARIES} Threads::Threads)

#add_executable(${PROJECT_NAME} main.cpp)
if (${CMAKE_SYSTEM_NAME} STREQUAL "iOS")
    set(CMAKE_OSX_DEPLOYMENT_TARGET "14.0")
endif ()