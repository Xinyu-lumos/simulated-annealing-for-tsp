cmake_minimum_required(VERSION 3.7)
project(tspsa)
# 设置策略
cmake_policy(SET CMP0078 NEW)
cmake_policy(SET CMP0086 NEW)


# 设置Numpy的相关变量
set(PYTHON_NUMPY_INCLUDE_DIR "/home/ying/.local/lib/python3.8/site-packages/numpy/core/include")

# 将Numpy的头文件路径添加到包含目录中
include_directories(${PYTHON_NUMPY_INCLUDE_DIR})

# 添加你的其他配置和构建步骤


set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS -O3)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/modules/")



FIND_PACKAGE(PythonLibs)

if (PYTHONLIBS_FOUND)
    FIND_PACKAGE(SWIG REQUIRED)
    INCLUDE(${SWIG_USE_FILE})

    INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})

    # FIND_PACKAGE(Numpy REQUIRED)
    # INCLUDE_DIRECTORIES(${PYTHON_NUMPY_INCLUDE_DIR})
endif()

include_directories( . )

# build library
set(SOURCE_FILES tsp_sa.cpp tsp_sa_solution.cpp tsp_log.cpp)
add_library(tspsa SHARED ${SOURCE_FILES})

# build executable
set(EXECUTABLE_FILES main.cpp)
add_executable(tspsa_exe ${EXECUTABLE_FILES})
target_link_libraries(tspsa_exe tspsa)

# build python library
if (PYTHONLIBS_FOUND)
    SET(CMAKE_SWIG_FLAGS "")
#    SET_SOURCE_FILES_PROPERTIES(SOURCE tsp_sa.i PROPERTY CPLUSPLUS ON)
#    SET_SOURCE_FILES_PROPERTIES(SOURCE tsp_sa.i PROPERTY SWIG_FLAGS "-includeall")
    SWIG_ADD_LIBRARY(pytspsa LANGUAGE python SOURCES tsp_sa.i)


    SWIG_LINK_LIBRARIES(pytspsa tspsa ${PYTHON_LIBRARIES})
endif()
