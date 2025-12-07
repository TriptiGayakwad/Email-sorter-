cmake_minimum_required(VERSION 3.20)
project(EmailAutoSorter VERSION 1.0.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Set build type if not specified
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# Compiler-specific options
if(MSVC)
    add_compile_options(/W4 /WX)
else()
    add_compile_options(-Wall -Wextra -Wpedantic -Werror)
endif()

# Enable testing
enable_testing()

# Find required packages
find_package(PkgConfig REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(CURL REQUIRED)

# External dependencies
include(FetchContent)

# nlohmann/json for JSON handling
FetchContent_Declare(
    nlohmann_json
    GIT_REPOSITORY https://github.com/nlohmann/json.git
    GIT_TAG v3.11.3
)

# Catch2 for unit testing
FetchContent_Declare(
    Catch2
    GIT_REPOSITORY https://github.com/catchorg/Catch2.git
    GIT_TAG v3.4.0
)

# RapidCheck for property-based testing
FetchContent_Declare(
    rapidcheck
    GIT_REPOSITORY https://github.com/emil-e/rapidcheck.git
    GIT_TAG master
)

# XGBoost for machine learning
FetchContent_Declare(
    xgboost
    GIT_REPOSITORY https://github.com/dmlc/xgboost.git
    GIT_TAG v2.0.3
)

FetchContent_MakeAvailable(nlohmann_json Catch2 rapidcheck xgboost)

# Include directories
include_directories(${CMAKE_SOURCE_DIR}/include)
include_directories(${CMAKE_SOURCE_DIR}/src)

# Add subdirectories
add_subdirectory(src)
add_subdirectory(tests)

# Main executable
add_executable(email_auto_sorter src/main.cpp)
target_link_libraries(email_auto_sorter 
    email_sorter_lib
    OpenSSL::SSL 
    OpenSSL::Crypto
    CURL::libcurl
    nlohmann_json::nlohmann_json
)

# Install targets
install(TARGETS email_auto_sorter DESTINATION bin)
install(DIRECTORY config/ DESTINATION etc/email-auto-sorter)