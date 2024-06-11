# RISCOF Tool Setup
- Original Setup Reference: From (Compliance Docker File)[https://gitlab.com/incoresemi/docker-images/-/blob/master/compliance/Dockerfile?ref_type=heads]

## Setup
```
export TOOL_HOME=<path>/tools
mkdir -p $TOOL_HOME
```

## Dependencies

```
sudo apt update
sudo apt install -y --no-install-recommends autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev \
  libgmp-dev gawk build-essential bison flex texinfo gperf libtool device-tree-compiler \
  patchutils bc zlib1g-dev libexpat-dev python3-pip libusb-1.0 \
  opam build-essential libgmp-dev z3 pkg-config zlib1g-dev \
  git make g++ wget curl
```

## Sail installation
```
opam init -y --disable-sandboxing 
opam switch create 5.1.0 
opam install sail -y 
eval $(opam env)  
cd $TOOL_HOME  
git clone https://github.com/NXP/sail-riscv.git -b zilsd  
cd sail-riscv  
ARCH=64 make c_emulator/riscv_sim_RV64  
ARCH=32 make c_emulator/riscv_sim_RV32
export PATH=$TOOL_HOME/sail-riscv/c_emulator:$PATH
```

## Spike installation
```
export RISCV=$TOOL_HOME/spike_zilsd
mkdir -p $RISCV
cd $TOOL_HOME 
git clone https://github.com/NXP/riscv-isa-sim.git -b -zilsd 
cd $TOOL_HOME/riscv-isa-sim 
git submodule update --init --recursive 
mkdir -p $TOOL_HOME/riscv-isa-sim/build 
cd $TOOL_HOME/riscv-isa-sim/build 
../configure --prefix=$RISCV 
make -j $(nproc)  1>/dev/null  
make install
export PATH=$TOOL_HOME/spike_zilsd/bin:$PATH
export LD_LIBRARY_PATH=$TOOL_HOME/spike_zilsd/lib:$LD_LIBRARY_PATH
```

## RISC-V GNU toolchain
```
export RISCV=$TOOL_HOME/riscv
mkdir -p $RISCV
cd $TOOL_HOME
git clone https://github.com/riscv/riscv-gnu-toolchain -b 2021.04.23 
cd riscv-gnu-toolchain 
git submodule init 
sed -i '/qemu/d' .gitmodules 
sed -i '/qemu/d' .git/config 
git submodule update -j $(nproc) --recursive 
./configure --prefix=$RISCV --with-arch=rv64gc --with-abi=lp64d 
make -j $(nproc) 1>/dev/null 
make clean 
./configure --prefix=$RISCV --with-arch=rv32gc --with-abi=ilp32d 
make -j $(nproc) 1>/dev/null 
make clean 
export PATH=$TOOL_HOME/riscv/bin:$PATH
export LD_LIBRARY_PATH=$TOOL_HOME/riscv/lib:$LD_LIBRARY_PATH
```

## LLVM Binary

```
cp -r llvm $TOOL_HOME
export PATH=$TOOL_HOME/llvm/bin:$PATH
```

## Python packages

### Python dependency setup:

```sh
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
    xz-utils tk-dev libffi-dev liblzma-dev git curl

curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

### Add the following at the end of ~/.bashrc

```sh
 export PATH="/home/$USER/.pyenv/bin:$PATH"
 eval "$(pyenv init -)"
 eval "$(pyenv init --path)"
 eval "$(pyenv virtualenv-init -)"
 ```

### For python 3.8

```sh 
CONFIGURE_OPTS=--enable-shared pyenv install 3.8.10
pyenv virtualenv 3.8.10 py38
```

### Activate env
```sh
pyenv activate py38
```

### Clone and install packages
```
cd $TOOL_HOME
git clone https://github.com/vyoma-systems/zilsd -b zilsd
cd zilsd
pip install reporg; reporg -d $PWD/wd --list $PWD/riscof_repo_list.yaml
pip install git+https://github.com/vyoma-systems/riscof.git@zilsd
```

### Compile and Run tests
```
riscof -v debug run --config=config.ini --suite=./wd/riscv-arch-test/riscv-test-suite/ --env=./wd/riscv-arch-test/riscv-test-suite/env
```
