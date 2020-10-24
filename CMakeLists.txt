cmake_minimum_required(VERSION 3.10)

project(build_libtorch)

set(PYTORCH_SRC_DIR ../pytorch)

include(CMakeDependentOption)
option(RESET "Restore and modify PyTorch source tree")
option(NO_BUILD_SHARED_LIBS "Do not build shared libraries")
set(ENV{DEBUG} 1) #will enable debug builds (-g -O0)
set(ENV{BUILD_TEST} 0) # will disable building C++ test binaries
set(ENV{USE_CUDA} 0) #disable compiling CUDA, to save compile time
set(ENV{USE_DISTRIBUTED} 0) #disable distributed (c10d, gloo, mpi, etc.) build
set(ENV{USE_MKLDNN} 0) #disable using MKL-DNN
set(ENV{USE_FBGEMM} 0) #disable using FBGEMM (quantized 8-bit server operators)
set(ENV{USE_NNPACK} 0) #disable compiling with NNPACK
set(ENV{USE_QNNPACK} 0) #disable QNNPACK build (quantized 8-bit operators)
set(ENV{USE_XNNPACK} 0) #disable compiling with XNNPACK
set(ENV{BUILD_PYTHON} 0) #Build Python binaries
set(ENV{BUILD_CAFFE2_OPS} 0) #Build Caffe2 operators

if(RESET)
  find_package(Git)
  if(Git_FOUND)
    execute_process(COMMAND ${GIT_EXECUTABLE} restore :/ WORKING_DIRECTORY ${PYTORCH_SRC_DIR} RESULT_VARIABLE RESULTV OUTPUT_VARIABLE OUTPUTV ERROR_VARIABLE ERRORV)
    execute_process(COMMAND ${GIT_EXECUTABLE} clean -df WORKING_DIRECTORY ${PYTORCH_SRC_DIR} RESULT_VARIABLE RESULTV OUTPUT_VARIABLE OUTPUTV ERROR_VARIABLE ERRORV)
  endif()
endif()

file(READ ${PYTORCH_SRC_DIR}/CMakeLists.txt FILE_CONTENTS)
if(NO_BUILD_SHARED_LIBS)
  string(FIND ${FILE_CONTENTS} "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\" ON)" POS)
	message("xxx1 ${POS}")
  if(NOT ${POS} EQUAL -1)
    string(REPLACE "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\" ON)" "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\")" FILE_CONTENTS "${FILE_CONTENTS}")
    file(WRITE ${PYTORCH_SRC_DIR}/CMakeLists.txt "${FILE_CONTENTS}")
  endif()
else()
  string(FIND ${FILE_CONTENTS} "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\"" POS)
  if(NOT ${POS} EQUAL -1)
    string(REPLACE "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\")" "option(BUILD_SHARED_LIBS \"Build libcaffe2.so\" ON)" FILE_CONTENTS "${FILE_CONTENTS}")
    file(WRITE ${PYTORCH_SRC_DIR}/CMakeLists.txt "${FILE_CONTENTS}")
  endif()
endif()

string(FIND ${CMAKE_CXX_FLAGS} "-O" O_FLAG_POS)
if(NOT O_FLAG_POS EQUAL -1)
  string(SUBSTRING ${CMAKE_CXX_FLAGS} ${O_FLAG_POS} 3 O_FLAG)
  if(NOT ${O_FLAG} STREQUAL '-O2')
    file(READ ${PYTORCH_SRC_DIR}/CMakeLists.txt FILE_CONTENTS)
    string(REPLACE "-O2" ${O_FLAG} FILE_CONTENTS "${FILE_CONTENTS}")
    file(WRITE ${PYTORCH_SRC_DIR}/CMakeLists.txt "${FILE_CONTENTS}")
  endif()
endif()

find_package(Python)
if(Python_Interpreter_FOUND)
  execute_process(COMMAND ${Python_EXECUTABLE} tools/build_libtorch.py WORKING_DIRECTORY ${PYTORCH_SRC_DIR})
endif()
