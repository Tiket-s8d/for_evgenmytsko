cmake_minimum_required(VERSION 3.26)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/toolchain.cmake)

project(MyApp C ASM)

file(GLOB MAIN_SRC "${CMAKE_SOURCE_DIR}/Core/src/*.c")
file(GLOB DRIVERS_SRC "${CMAKE_SOURCE_DIR}/Drivers/STM32F1xx_HAL_Driver/Src/*.c")


add_executable(${PROJECT_NAME} ${MAIN_SRC} ${DRIVERS_SRC} startup_stm32f103xb.s)

target_include_directories(${PROJECT_NAME} PRIVATE 
    ${CMAKE_SOURCE_DIR}/Core/Inc 
    ${CMAKE_SOURCE_DIR}/Drivers/STM32F1xx_HAL_Driver/Inc 
    ${CMAKE_SOURCE_DIR}/Drivers/STM32F1xx_HAL_Driver/Inc/Legacy 
    ${CMAKE_SOURCE_DIR}/Drivers/CMSIS/Device/ST/STM32F1xx/Include 
    ${CMAKE_SOURCE_DIR}/Drivers/CMSIS/Include    
)


set(ELF_OUTPUT_FILE ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.elf)
set(HEX_OUTPUT_FILE ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.hex)
set(BIN_OUTPUT_FILE ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${PROJECT_NAME}> ${ELF_OUTPUT_FILE}
    COMMAND ${CMAKE_OBJCOPY} -O ihex $<TARGET_FILE:${PROJECT_NAME}> ${HEX_OUTPUT_FILE}
    COMMAND arm-none-eabi-objcopy -O binary ${ELF_OUTPUT_FILE} ${BIN_OUTPUT_FILE}
)