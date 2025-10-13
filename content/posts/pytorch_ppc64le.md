# Compilling PyTorch on Power9 ppc64le

## 1. Environment Preparation

```bash
# Prepare Environment
## Create Python Environment
micromamba create -y -n pytorch python=3.12

## Create Folders
mkdir -p ~/Software/ppc64le/magma
mkdir -p ~/Wheels/ppc64le

## Set qQuad Precision (Float128)
export CFLAGS="-mfloat128"
export LDFLAGS="-lquadmath"
```

## 2. Installing Magma

```bash
# Magma
cd ~/Wheels/ppc64le

## Clone Magma (v2.9.0)
git clone https://github.com/icl-utk-edu/magma.git
cd magma
git checkout v2.9.0

## Config Magma
cd magma
echo -e 'BACKEND = cuda\nGPU_TARGET = Volta Turing' > make.inc
make generate

## Build & Install Magma
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=~/Software/ppc64le/magma -DGPU_TARGET='sm_70;sm_75' ..
make -j16
make install
```

## 3. Installing PyTorch

```bash
# PyTorch
cd ~/Wheels/ppc64le

## Clone PyTorch (v2.8.0)
git clone https://github.com/pytorch/pytorch.git
cd pytorch
git checkout v2.8.0

## Git Pull SubModule
git submodule sync
git submodule update --init --recursive

## Install CMake and Ninja
micromamba install cmake ninja
pip install -r requirements.txt
```

Create File .ci/docker/common/install_magma_ppc64le.sh

```bash
#!/usr/bin/env bash
set -eou pipefail

cuda_version_nodot=${1/./}
anaconda_dir=${CONDA_PREFIX:-"$(dirname $(which micromamba))/../"}

(
    cp -r /users/congye/Software/ppc64le/magma/include/* "${anaconda_dir}/include/"
    cp -r /users/congye/Software/ppc64le/magma/lib/* "${anaconda_dir}/lib"
)
```

Continue running the command

```bash
## Install Magma to Virtual Environment
.ci/docker/common/install_magma_ppc64le.sh 12.4

## Install PyTorch
export CMAKE_PREFIX_PATH="${CONDA_PREFIX:-'$(dirname $(which micromamba))/../'}:${CMAKE_PREFIX_PATH}"
python setup.py develop
```
