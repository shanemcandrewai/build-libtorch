# PyTorch libtorch minimal using build_libtorch.py

This CMakeLists.txt manages the building of a minimal libtorch from [PyTorch 1.6](https://github.com/pytorch/pytorch/tree/1.6) by setting environment variables and calling [pytorch/tools/build_libtorch.py](https://github.com/pytorch/pytorch/blob/v1.6.0/tools/build_libtorch.py)
## Prerequisites

1. Clone [PyTorch 1.6](https://github.com/pytorch/pytorch/tree/1.6) and adjust the [CMakeLists.txt](CMakeLists.txt) variable `PYTORCH_SRC_DIR` to point to the local repository, for example `set(PYTORCH_SRC_DIR ../pytorch)`

2. Install the [PyTorch prerequisites](https://github.com/pytorch/pytorch/tree/1.6#from-source)

## Equivalent bash script
    cd pytorch
    git restore :/
    git clean -df
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
Restores and cleans the PyTorch source working tree from HEAD. This can be enabled by passing the option `-D RESET=1`.
###### NO_BUILD_SHARED_LIBS (dependent on RESET)
The build generates a shared library by default. This can be disabled by passing the option `-D RESET=1 -D NO_BUILD_SHARED_LIBS=1`.
#### Example
    cmake -DRESET=1 -S . -B build
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
