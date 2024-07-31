---
title: "Install GCC 6.3.0 on Ubuntu"
date: "31 Jul 2024"
---


```bash
# Create GCC Storage Directory
sudo mkdir /usr/local/gcc

# Download the Source Code of GCC6.3.0
cd ~/Downloads
wget https://ftp.gnu.org/gnu/gcc/gcc-6.3.0/gcc-6.3.0.tar.gz

# Decompression
tar -zxf gcc-6.3.0.tar.gz

# Download and Compile Dependencies
./contrib/download_prerequisites

# Configuring Compilation Options
./configure --prefix=/usr/local/gcc/gcc-6.3.0 --enable-threads=posix --disable-checking --disable-multilib --enable-languages=c,c++ --disable-libsanitizer

# Compile the Source Code
make

# Fix GCC6.3.0 Source Compile Error
# error: dereferencing pointer to incomplete type ‘struct ucontext’ (GCC8.1 does not have this error).
vim ./x86_64-pc-linux-gnu/libgcc/md-unwind-support.h

# Skip to Line 61 and Replace with
struct ucontext_t *uc_ = context->cfa;

# Installing
sudo make install

# View system GCC and G++ locations
which gcc

# Adding Common Versions to the GCC and G++ Version Managers
sudo update-alternatives  --install /usr/bin/gcc gcc /usr/bin/gcc-11 110
sudo update-alternatives  --install /usr/bin/g++ g++ /usr/bin/g++-11 110

# Setting Up a Soft Link for GCC-6.3.0
sudo ln -s /usr/local/gcc/gcc-6.3.0/bin/gcc /usr/bin/gcc-6
sudo ln -s /usr/local/gcc/gcc-6.3.0/bin/g++ /usr/bin/g++-6

# Add GCC-6.3.0 to Version Manager
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-6 60

# Switch the Current GCC Version
sudo update-alternatives --config gcc
sudo update-alternatives --config g++

# Check the GCC Version of the System
gcc --version
g++ --version
```

