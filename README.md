# PyTorch libtorch minimal using build_libtorch.py
This CMakeLists.txt manages the building of a minimal libtorch from [PyTorch 1.6](https://github.com/pytorch/pytorch/tree/1.6) by setting environment variables and calling [pytorch/tools/build_libtorch.py](https://github.com/pytorch/pytorch/blob/v1.6.0/tools/build_libtorch.py)
## Important note
PyTorch is working on eliminating tools/build_pytorch_libs.sh in favor of a unified cmake build [source](https://github.com/pytorch/pytorch/blob/v1.6.0/docs/libtorch.rst)
## Prerequisites
1. Clone [PyTorch 1.6](https://github.com/pytorch/pytorch/tree/1.6) and adjust the [CMakeLists.txt](CMakeLists.txt) variable `PYTORCH_SRC_DIR` to point to the local repository, for example `set(PYTORCH_SRC_DIR ../pytorch)`
2. Install the [PyTorch prerequisites](https://github.com/pytorch/pytorch/tree/1.6#from-source)
## Equivalent bash script
    cd pytorch
    git restore :/
    git clean -dfx
    export DEBUG=1
    export BUILD_TEST=0
    export USE_CUDA=0
    export USE_DISTRIBUTED=0
    export USE_MKLDNN=0
    export USE_FBGEMM=0
    export USE_NNPACK=0
    export USE_QNNPACK=0
    export USE_XNNPACK=0
    export BUILD_PYTHON=0
    export BUILD_CAFFE2_OPS=0
    python tools/build_libtorch.py
## Usage
### Generate and build
    cmake -S . -B build
#### Cmake options
##### RESET
Restore and clean the PyTorch source working tree from HEAD.
##### NO_BUILD_SHARED_LIBS
The build generates a shared library by default. This can be disabled by passing the option `-D NO_BUILD_SHARED_LIBS=1`.
##### USE_STATIC_DISPATCH
The build does not use static dispatch for ATen operators by default. This can be enabled by passing the option `-D USE_STATIC_DISPATCH=1`.
##### CMAKE_BUILD_TYPE 
The default buid type is `Release`. For a debug build pass option `-D CMAKE_BUILD_TYPE=Debug`
##### CMAKE_CXX_FLAGS
###### GCC
`-Og` enables optimizations that do not interfere with debugging
#### Example
    cmake -DRESET=1 -DCMAKE_CXX_FLAGS=-Og -DCMAKE_BUILD_TYPE=Debug -S . -B build
### Location of built libraries
    pytorch/build/lib
### Cleaning / trouble-shooting
#### Linux
    rm build/CMakeCache.txt
    rm -rf build
#### Windows
    del build/CMakeCache.txt
    rmdir /s /q build
    mklink /d build d:\build
#### Clean PyTorch source without using standard ignore rules
    git clean -dfx
