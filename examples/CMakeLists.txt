cmake_minimum_required(VERSION 3.12)

# Pull in SDK (must be before project)
include(pico_sdk_import.cmake)

project(TS-UNB-Lib C CXX ASM)


set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

add_executable(sendString sendString.cpp)
target_link_libraries(sendString hardware_spi pico_stdlib)

if (NOT EXISTS "${CMAKE_SOURCE_DIR}/.env") 
    message(FATAL_ERROR "Please create a .env file in the project root directory and set the following variables: 
    MAC_NETWORK_KEY and MAC_EUI64. Example content of .env file:\n\nMAC_NETWORK_KEY=your_network_key\nMAC_EUI64=your_eui64")
else()
    file(STRINGS .env MAC_NETWORK_KEY REGEX "MAC_NETWORK_KEY=")
    file(STRINGS .env MAC_EUI64 REGEX "MAC_EUI64=")
    file(STRINGS .env MAC_SHORT_ADDR REGEX "MAC_SHORT_ADDR=")
    string(REPLACE "MAC_NETWORK_KEY=" "" MAC_NETWORK_KEY ${MAC_NETWORK_KEY})
    string(REPLACE "\"" "" MAC_NETWORK_KEY ${MAC_NETWORK_KEY})
    string(CONCAT MAC_NETWORK_KEY "MAC_NETWORK_KEY=" ${MAC_NETWORK_KEY})
    string(REPLACE "MAC_EUI64=" "" MAC_EUI64 ${MAC_EUI64})
    string(REPLACE "\"" "" MAC_EUI64 ${MAC_EUI64})
    string(CONCAT MAC_EUI64 "MAC_EUI64=" ${MAC_EUI64})
    string(REPLACE "MAC_SHORT_ADDR=" "" MAC_SHORT_ADDR ${MAC_SHORT_ADDR})
    string(REPLACE "\"" "" MAC_SHORT_ADDR ${MAC_SHORT_ADDR})
    string(CONCAT MAC_SHORT_ADDR "MAC_SHORT_ADDR=" ${MAC_SHORT_ADDR})
    message(STATUS "MAC_NETWORK_KEY define: ${MAC_NETWORK_KEY}")
    message(STATUS "MAC_EUI64       define: ${MAC_EUI64}")
    message(STATUS "MAC_SHORT_ADDR  define: ${MAC_SHORT_ADDR}")
    target_compile_definitions(sendString PRIVATE ${MAC_NETWORK_KEY} ${MAC_EUI64} ${MAC_SHORT_ADDR})
endif()

# Initialize the SDK
pico_sdk_init()

pico_add_extra_outputs(sendString)

