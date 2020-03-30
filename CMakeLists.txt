cmake_minimum_required(VERSION 3.2 FATAL_ERROR)
set(CMAKE_VERBOSE_MAKEFILE ON)

# Import cmake modules
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/arm-toolchain")
message(STATUS "CMake module path: ${CMAKE_MODULE_PATH}")

add_compile_definitions(CPU_LPC845 CPU_LPC845M301JHI33 __USE_CMSIS __NEWLIB__ __MCUXPRESSO NDEBUG)
add_compile_definitions(SDK_DEBUGCONSOLE=DEBUGCONSOLE_DISABLE)

# Use Cortex M compiler
include(cortex-m0plus)

project(lpc)

# Define source files for library
set(LPC_STARTUP_SRC device/lpc845/startup_lpc845.c)
set(LPC_DEVICE_SRC device/lpc845/system_LPC845.c)
set(LPC_MIN_DRIVERS drivers/fsl_clock.c drivers/fsl_common.c drivers/fsl_gpio.c drivers/fsl_power.c drivers/fsl_reset.c drivers/fsl_crc.c)
set(LPC_BASIC_DRIVERS drivers/fsl_iap.c drivers/fsl_i2c.c drivers/fsl_swm.c)

set(LPC_INCLUDE device device/lpc845 cmsis drivers)
include_directories(${LPC_INCLUDE})

set(LIBRARY_MIN_SRC ${LPC_STARTUP_SRC} ${LPC_DEVICE_SRC} ${LPC_MIN_DRIVERS})
set(LIBRARY_SRC ${LIBRARY_MIN_SRC} ${LPC_BASIC_DRIVERS})

# Build library
add_library(lpcmin STATIC ${LIBRARY_MIN_SRC})
link_directories(${LIBRARY_MIN_SRC})
target_include_directories(lpcmin PUBLIC ${LPC_INCLUDE})

add_library(lpc STATIC ${LPC_BASIC_DRIVERS})
target_link_libraries(lpc lpcmin)
link_directories(${LIBRARY_SRC})
target_include_directories(lpc PUBLIC ${LPC_INCLUDE})
