# Compile-2Ship2Harkinian-for-Raspberry-Pi-1.1.2-Sakoto-Charlie-Release.-[2025]
The steps described to compile 2ship2harkinian port of " The Legend of Zelda Majora's Mask".
Credits to the team of HarbourMasters for making it possible.
https://github.com/HarbourMasters/2ship2harkinian

## Also you can download the distributable version up here. Download > download.zip 

## 1. Prerequisites:

- Raspberry Pi 5 (Have not tested any older model) Raspberry Pi OS 64-bit (Debian Bookworm)
- Internet connection.
- At least 4GB of ram available (recomended), but in my experience the raspberry pi never used more than 1.5 - 2 GB to compile.

---

## 2. Installing the necessary dependencies:

```bash
sudo apt-get update && sudo apt upgrade -y
sudo apt-get install -y \
  build-essential \
  git \
  lsb-release \
  ninja-build \
  gcc g++ \
  # SDL2 and network
  libsdl2-dev libsdl2-net-dev \
  # PNG
  libpng-dev \
  # ZIP: development and user tool
  libzip-dev libzip-tools zip \
  # JSON, XML, logging
  nlohmann-json3-dev libtinyxml2-dev libspdlog-dev \
  # Boost (all metapack)
  libboost-dev \
  # OpenGL (mesa)
  mesa-common-dev \
  # (opcional) Python3 dev if you use modules
  python3-dev
```

## Debian bookworm 
- In raspberry pi, by default comes with cmake 3.25._ version, but to compile 2s2h we need cmake 3.26._ or above versions. Looking for the package the actual cmake is 3.28._ wich in my case, it worked. So there are two options, intalling a pre-compiled version or compile our own.
In this case I will describe the steps to compile your own version:

1) Prepare the enviroment:
```
sudo apt-get install -y \
  build-essential \
  libssl-dev \
  libncurses5-dev libncursesw5-dev \
  libcurl4-openssl-dev \
  zlib1g-dev
```
2) Download cmake 3.28.0:
```
cd /tmp
wget https://github.com/Kitware/CMake/releases/download/v3.28.0/cmake-3.28.0.tar.gz
# decompresse it
tar xf cmake-3.28.0.tar.gz
cd cmake-3.28.0
```
3) Configure and compile:
Use this two commands one by one.
```
./bootstrap --parallel=$(nproc)

make -j$(nproc)
```
- Notice compiling may take a while depending on your pi. Mine is RPi 5  with 8GB of ram and it took like 10-15 minutes to compile.
- So you can go a make some coffe. In the same way, 2s2h will take even a bit more minutes to compile.

4) Install cmake 3.28
```
sudo make install
```
5) This an extra but mandatory step, cmake 3.25 will be by default the one the os uses. So we will need to change it for the new compiled version:
```
# Optional, check your version:
cmake --version
# Using cmake --version you will notice that it may have a conflict with your older version.
# So you can change it temporarly just for compilig by using:

export PATH=/opt/cmake-3.28/bin:$PATH

# Then Reload:

source ~/.bashrc

# Check if your version is 3.28

cmake --version

# You should see:
# cmake version 3.28.0 or similar.
```

## The final steps, compiling 2ship2harkinian. Take in consideration that compiling may take a while. Like 20-30 minutes. So be patient.

1) Clone the git repository:
```
git clone https://github.com/HarbourMasters/2ship2harkinian.git
cd 2ship2harkinian
```

2) Initialize submodules:
```
git submodule update --init --recursive
```

3) Generate the ninja project:
```
cmake -H. -Bbuild-cmake -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DPython3_EXECUTABLE=$(which python3)
```

4)  Generate the assets (2ship.o2r)
```
cmake --build build-cmake --target Generate2ShipOtr
```

5) finally compile everything: [Remeber this step may take a while depending on your hardware]
```
cmake --build build-cmake
```

# 6. Created Executable:
```
./build-cmake/mm/2s2h.elf
```
You can execute it by cd into the folder where 2s2h.elf is (the executable) and it will ask for your ROM.

## Note:
At this point we are almost done, you made the executable for 2s2h, but remember that you need a legally adquired ROM, you can use the page provided by the HarbourMasters git to check if the rom is compatible for use:
So you can check your ROM here:
https://2ship.equipment/

- Then if your ROM is compatible, you can just drop it on /build-cmake/mm/2s2h.elf, or well click on "Search for a ROM" and search the folder where your ROM is.
- After that the progam will start to do some extra compiling and once it is done, it will run showing you a logo of "libustraship", wich basically means it is done and you can play it now.

## Mods:
Now if you want to add mods, such as "MM-Reloaded" wich adds hd textures (4k is not yet recomended). You can download it and add them to your /mm/mods folder. You can check the few mods available here:
https://gamebanana.com/games/20371

## Clean
- Finally if space is crucial (whether if not) you can clean everything and just keep the port, remeber to be in the folder where you build the project cd/2ship2harkinian/..:
```
cmake --build build-cmake --target clean
```
- Optional, if you need to regenerate the asset headers to check them into source:
```
cmake --build build-cmake --target ExtractAssetHeaders
```
