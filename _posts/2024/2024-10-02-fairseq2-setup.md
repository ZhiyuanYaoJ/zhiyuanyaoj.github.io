---
title: Reproducible Setup for Fairseq2
date: 2024-10-02
description: This blog documents the steps to set up a reproducible environment for Fairseq2.
categories:
- Python
tags:
- Python
- Fairseq2
photos:
---

## Step 1: System Update and Python Setup

To begin the setup process, ensure that the system was up to date and prepared for the installation of the necessary tools and libraries. 

```bash
sudo apt update
sudo apt upgrade
```

Install Python 3.12 if not installed (I picked 3.12 but >=3.10 and <=3.12 should work):

```bash
sudo apt install software-properties-common
#add the deadsnakes PPA:
sudo add-apt-repository ppa:deadsnakes/ppa
# install
sudo apt install python3.12
# to verify
python3.12 --version
```

## Step 2: Creating a Virtual Environment

Once the correct Python version was verified, I proceeded to create a virtual environment to isolate dependencies specific to Fairseq2 and Seamless Communication. This helps maintain a clean environment and prevents conflicts with other projects.

To guarantee that the environment is free from potential pip issues, I created the virtual environment without pip and proceeded to install pip manually:

```bash
python3.12 -m venv --without-pip venv_py3_12
```

After setting up the environment, I activated it:
```bash
source venv_py3_12/bin/activate
```

## Step 3: Installing Pip

At this point, I needed to manually install pip. I used `curl` to download the pip installation script and then ran it within the virtual environment:
```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
rm -f get-pip.py
```

Finally, I confirmed the installation by checking the pip version:
```bash
python3.12 -m pip --version
```

## Step 4: Installing Fairseq2 from Source

Follow the instructions [here](https://github.com/facebookresearch/fairseq2/blob/main/INSTALL_FROM_SOURCE.md) to install FairSeq2 from source.

To get the repo and install from source:
```bash
git clone --recurse-submodules https://github.com/facebookresearch/fairseq2.git
```

### Clean up and install the right version of CUDA

Fairseq2 requires CUDA with specific versions ([ref](https://github.com/facebookresearch/fairseq2?tab=readme-ov-file#variants)). Please first check if the version of CUDA installed on your system is compatible with Fairseq2.

```bash
# both should return the same version
nvcc --version
nvidia-smi
```

If the versions are not compatible, follow the instructions below to clean up and install the right version of CUDA. Otherwise, you can skip the steps below.

Check the instructions for the right version of CUDA and clean up cuda:
```bash
sudo apt-get purge nvidia*
sudo apt-get autoremove
sudo apt-get autoclean
sudo rm -rf /usr/local/cuda*
```

Install the right version of cuda (_e.g._ 12.1, more instructions [here](https://developer.nvidia.com/cuda-12-1-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=runfile_local)):
```bash
wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda_12.1.0_530.30.02_linux.run
sudo sh cuda_12.1.0_530.30.02_linux.run
```

Add the following to your `.bashrc` or `.zshrc`:
```bash
# Set up CUDA environment variables
export PATH=/usr/local/cuda-12.1/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.1/lib64:$LD_LIBRARY_PATH
```

To verify the installation, run:
```bash
nvidia-smi
```

### Handle CMake

Verify the version of CMake installed on your system (I installed 3.30):
```bash
# should return >3.27
cmake --version
```

If the version is not compatible with Fairseq2, you can install from source.

```bash
# cleanup
sudo apt remove --purge --auto-remove cmake

# install from source
version=3.30
build=4
## don't modify from here
mkdir ~/temp
cd ~/temp
wget https://cmake.org/files/v$version/cmake-$version.$build.tar.gz
tar -xzvf cmake-$version.$build.tar.gz
cd cmake-$version.$build/

# install
./bootstrap
make -j$(nproc)
sudo make install

# verify
cmake --version

# cleanup
cd ~/
rm -rf temp
```

### Build fairseq2n

```bash
# insall dependencies
sudo apt install ninja-build

cd native
# see more notes below for the cmake command (ref 0x00)
cmake -GNinja -DFAIRSEQ2N_USE_CUDA=ON -DPython3_INCLUDE_DIRS=/usr/include/python3.12 -DCMAKE_CUDA_COMPILER=/usr/local/cuda-12.1/bin/nvcc -DCMAKE_CUDA_ARCHITECTURES="75-real;75-virtual" -B build

# optionally, if you see the error of no `libcudart.so` found in `/usr/lib/x86_64-linux-gnu/`, you can copy it from the cuda folder (it's just a symlink) (ref 0x01)
sudo cp /usr/local/cuda-12.1/targets/x86_64-linux/lib/libcudart.so /usr/lib/x86_64-linux-gnu/

# build
cmake --build build --clean-first

# install
cd python
pip install -e .

# in case you see the error: cannot import name 'FileError' from 'setuptools.errors'
pip install --upgrade testresources setuptools
```


A couple of notes:
- 0x00
   - `-DFAIRSEQ2N_USE_CUDA=ON` is the key flag to use CUDA
   - `-DPython3_INCLUDE_DIRS=/usr/include/python3.12` is the key flag to use Python 3.12
   - `-DCMAKE_CUDA_COMPILER=/usr/local/cuda-12.1/bin/nvcc` is the key flag to use CUDA 12.1
   - `-DCMAKE_CUDA_ARCHITECTURES="75-real;75-virtual"` is the key flag to use the Turing architecture (T4 GPUs). For Ampere architecture (A100 GPUs), use `-DCMAKE_CUDA_ARCHITECTURES="80"`. For the whole list of architectures, see [here](https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/).
- 0x01
   - if you see the error of no `libcudart.so` found in `/usr/lib/x86_64-linux-gnu/`, you can copy it from the cuda folder (it's just a symlink)

### Install Fairseq2

The following steps are relatively straightforward.

```bash
# install fairseq2n
cd native/python
pip install -e .

# install fairseq2
cd ../../
pip install -e . 

# optionally install requirements devel tool
pip install -r requirements-devel.txt
```

## Step 5: Sanity Check

Make sure that we have installed the packages correctly.

```bash
# install requirements
pip install -r requirements-devel.txt

# run the test (by default, run on CPU )
pytest

# run the test on GPU (only on cuda:0, otherwise some tests will fail)
pytest --device cuda:0
```
