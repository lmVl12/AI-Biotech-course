# Installing GROMACS 2025.4 on WSL (Ubuntu 22.04)

These notes document the actual installation process I went through — including the mistakes, fixes, and small discoveries along the way. Hopefully this helps someone avoid the same pitfalls.


---

## 0. Download the GROMACS source tarball
Before extracting the source, you must download the tarball. The official tutorial assumes the file is already present, but most users need to download it manually.
```bash
# Download the tarball
wget https://ftp.gromacs.org/gromacs/gromacs-2025.4.tar.gz

# Verify the file
ls gromacs-2025.4.tar.gz
```

---

## 1. Extract the source

```bash
# create a folder for gromacs in home directory and navigate there
cd ~
mkdir gromacs
mv gromacs-2025.4.tar.gz gromacs/
cd gromacs

#extract here
tar xfz gromacs-2025.4.tar.gz
cd gromacs-2025.4

```

---

## 2. Create a build directory

```bash

mkdir build
cd build
```

---

## 3. Configure with CMake

```bash
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
```

### Error: CMake 3.28 or higher is required  
Cause: Ubuntu 22.04 provides CMake 3.22, so a newer version must be installed manually.  
Solution: Install a modern CMake manually.

```bash
#move to home directory
cd ~

#download the latest version
wget https://github.com/Kitware/CMake/releases/download/v3.29.6/cmake-3.29.6-linux-x86_64.sh

#give the execution rights
chmod +x cmake-3.29.6-linux-x86_64.sh

#install
sudo ./cmake-3.29.6-linux-x86_64.sh --skip-license --prefix=/usr/local

# Add to PATH
echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Verify
cmake --version
```

Re-run CMake:

```bash
cd ~/gromacs/gromacs-2025.4/build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
```

### Error: No CMAKE_CXX_COMPILER could be found  
Cause: g++ was missing.  
Solution: I could install just g++, but to avoid more missing-tool surprises later, it is recommended to install the full build essentials.

```bash
sudo apt update
sudo apt install build-essential
```

Re-run CMake:

```bash
cd ~/gromacs/gromacs-2025.4/build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
```

This time configuration completed successfully

---

## 4. Compile

```bash
# use 4 CPU threads to speed up compilation (adjust to your CPU)
make -j4
```

---

## 5. Run tests

```bash
make check
```

---

## 6. Install

```bash
sudo make install
```

### Note: 
GROMACS ended up in `/usr/local/bin/` instead of `/usr/local/gromacs/`.  Not a problem, just something to be aware of.

---

## 7. Activate GROMACS

```bash
# Activate environment
source /usr/local/bin/GMXRC

# Final check
gmx --version
```

---

## Auto-activate on every terminal start

```bash
# Add activation to .bashrc so GROMACS is always available
echo 'source /usr/local/bin/GMXRC' >> ~/.bashrc
source ~/.bashrc
```
