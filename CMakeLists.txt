cmake_minimum_required(VERSION 3.10)

project(lv_port_linux C)

# Set the C standard
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Set the LVGL directory
set(LVGL_DIR ${CMAKE_CURRENT_SOURCE_DIR})

# Check for lv_conf.h and lv_drv_conf.h
if(NOT EXISTS "${LVGL_DIR}/lv_conf.h")
    message(FATAL_ERROR "lv_conf.h not found in the project directory. Please provide this file.")
endif()

if(NOT EXISTS "${LVGL_DIR}/lv_drv_conf.h")
    message(FATAL_ERROR "lv_drv_conf.h not found in the project directory. Please provide this file.")
endif()

# Add LVGL library
add_subdirectory(${LVGL_DIR}/lvgl)

# Add lv_drivers library
add_subdirectory(${LVGL_DIR}/lv_drivers)

# Define the executable
add_executable(demo
    ${LVGL_DIR}/main.c
    ${LVGL_DIR}/mouse_cursor_icon.c
)

# Include directories
target_include_directories(demo PRIVATE
    ${LVGL_DIR}
    ${LVGL_DIR}/lvgl
    ${LVGL_DIR}/lv_drivers
)

# Link libraries
target_link_libraries(demo PRIVATE lvgl lv_drivers lvgl::demos lvgl::examples)

# Find and link SDL2
find_package(SDL2 REQUIRED)
target_link_libraries(demo PRIVATE SDL2::SDL2)

# Link math library
target_link_libraries(demo PRIVATE m)

# Set compiler flags
target_compile_options(demo PRIVATE
    -O3 -g0 -Wall -Wshadow -Wundef -Wmissing-prototypes -Wno-discarded-qualifiers
    -Wall -Wextra -Wno-unused-function -Wno-error=strict-prototypes -Wpointer-arith
    -fno-strict-aliasing -Wno-error=cpp -Wuninitialized -Wmaybe-uninitialized
    -Wno-unused-parameter -Wno-missing-field-initializers -Wtype-limits
    -Wsizeof-pointer-memaccess -Wno-format-nonliteral -Wno-cast-qual -Wunreachable-code
    -Wno-switch-default -Wreturn-type -Wmultichar -Wformat-security -Wno-ignored-qualifiers
    -Wno-error=pedantic -Wno-sign-compare -Wno-error=missing-prototypes -Wdouble-promotion
    -Wclobbered -Wdeprecated -Wempty-body -Wtype-limits -Wshift-negative-value
    -Wstack-usage=2048 -Wno-unused-value -Wno-unused-parameter
    -Wno-missing-field-initializers -Wuninitialized -Wmaybe-uninitialized
    -Wall -Wextra -Wno-unused-parameter -Wno-missing-field-initializers -Wtype-limits
    -Wsizeof-pointer-memaccess -Wno-format-nonliteral -Wpointer-arith -Wno-cast-qual
    -Wmissing-prototypes -Wunreachable-code -Wno-switch-default -Wreturn-type
    -Wmultichar -Wno-discarded-qualifiers -Wformat-security -Wno-ignored-qualifiers
    -Wno-sign-compare
)

# Add define to use custom lv_conf.h
target_compile_definitions(lvgl PUBLIC LV_CONF_INCLUDE_SIMPLE)
target_compile_definitions(demo PRIVATE LV_CONF_INCLUDE_SIMPLE)

# Ensure lv_conf.h is found
target_include_directories(lvgl PUBLIC ${LVGL_DIR})
