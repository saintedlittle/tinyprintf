cmake_minimum_required(VERSION 3.10)
project(TinyPrintfTests)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -O3 -g")

# set(RUNNER "valgrind")

add_executable(test_printf test/printf.c tinyprintf.c)
add_executable(test_sprintf test/sprintf.c tinyprintf.c)

add_library(tinyprintf_minimal OBJECT tinyprintf.c)
target_compile_definitions(tinyprintf_minimal PRIVATE 
    TINYPRINTF_DEFINE_TFP_PRINTF=0
    TINYPRINTF_DEFINE_TFP_SPRINTF=0
    TINYPRINTF_OVERRIDE_LIBC=0
)

add_library(tinyprintf_only_tfp_printf OBJECT tinyprintf.c)
target_compile_definitions(tinyprintf_only_tfp_printf PRIVATE
    TINYPRINTF_DEFINE_TFP_PRINTF=1
    TINYPRINTF_DEFINE_TFP_SPRINTF=0
    TINYPRINTF_OVERRIDE_LIBC=0
)

add_library(tinyprintf_only_tfp_sprintf OBJECT tinyprintf.c)
target_compile_definitions(tinyprintf_only_tfp_sprintf PRIVATE
    TINYPRINTF_DEFINE_TFP_PRINTF=0
    TINYPRINTF_DEFINE_TFP_SPRINTF=1
    TINYPRINTF_OVERRIDE_LIBC=0
)

target_link_libraries(test_printf PRIVATE tinyprintf_minimal)
target_link_libraries(test_sprintf PRIVATE tinyprintf_minimal)

add_custom_target(runtests
        COMMAND ${RUNNER} $<TARGET_FILE:test_printf> || exit $$?
        COMMAND ${RUNNER} $<TARGET_FILE:test_sprintf> || exit $$?
)

add_custom_target(cleanup
    COMMAND ${CMAKE_COMMAND} -E remove -f test/printf test/sprintf
)
