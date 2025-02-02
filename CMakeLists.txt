cmake_minimum_required(VERSION 3.9.4)
project(riscv CXX)

option(SANITIZE    "Enable sanitizers" OFF)

set(SOURCES
	src/shmain.cpp
    src/register_types.cpp
	src/syscalls.cpp
)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

if (SANITIZE)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=address,undefined")
endif()

# Add godot-cpp library
add_subdirectory(godot-cpp)

# Add sandboxing library
add_subdirectory(libriscv/lib libriscv)
target_compile_definitions(riscv PUBLIC
	RISCV_SYSCALLS_MAX=600
	RISCV_BRK_MEMORY_SIZE=0x100000  # 1MB (we don't need much)
)


add_library(godot-riscv SHARED ${SOURCES})
target_link_libraries(godot-riscv PUBLIC riscv godot-cpp)
