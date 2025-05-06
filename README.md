# CPUMiner-ScashX

cpuminer-scashx is a free, high performance, open source, cross platform CPU miner for ScashX.

cpuminer-scashx has been tested on Linux and on Intel/AMD x86-64 and ARM64 processors.

ScashX uses RandomX 1.2.1 as its mining algorithm, as specified here: (https://github.com/scashx/scashx/blob/scashx_master/doc/scashx-protocol-spec.md)

## Features

Cpuminer supports:
- Solo mining with ScashX node (RPC getblocktemplate)
- Mining pools (Stratum V1 protocol)
- Hiveon OS

## Download
- Binary releases: https://github.com/scashx/cpuminer-scashx/releases
- Build from source (recommended)

## Hiveon OS instructions

Create a new flight sheet, select 'Custom miner', click 'Setup miner config' and then enter the following:
- Miner name: `cpuminer_scashx`
- Installation URL: Copy the URL of the latest hiveon binary in Github releases
- Hash algorithm: `----` (`randomx` and `randomscashx` can also be selected)
- Pool URL: `stratum+tcps://ADDRESS:PORT` (obtain address and port from mining pool)
- Wallet and worker template: `%WAL%.%WORKER_NAME%`
- Pass: `x` (or empty)
- Extra config arguments: `--largepages --quiet --no-affinity --threads=N` (run `minerd -h` to see full list of options)

## Build dependencies

Cpuminer depends on the following libraries:
- libcurl, https://curl.se/libcurl/
- jansson, https://github.com/akheron/jansson (jansson is included locally)
- RandomX, https://github.com/tevador/RandomX (RandomX is included as a Git submodule)

## Linux

To build for Ubuntu Linux (or WSL in Windows):

### Install dependencies
```
sudo apt update
sudo apt upgrade
sudo apt install autoconf pkg-config g++ make libcurl4-openssl-dev
```

### Build instructions
```
git clone https://github.com/scashx/cpuminer-scashx --recursive
cd cpuminer-scashx
./autogen.sh
./configure
make
```

### Build static binary

Run the script `build-linux-static.sh` which uses Docker to build a static binary for Linux in the `out` folder.
```
./build-linux-static.sh
out/minerd --version
```

The steps to create a static binary with Alpine Linux and musl are outlined in the `build-linux-static.dockerfile`, with mbedtls used as the libcurl SSL backend.

Using glibc to create a static binary is not recommended: https://stackoverflow.com/questions/57476533/why-is-statically-linking-glibc-discouraged/57478728#57478728

### Usage

Help message and options:
```
./minerd -h
```

Solo mine on 4 cpu threads, connected to a local ScashX node:
```
./minerd -o 127.0.0.1:8342 -O username:password -t 4 --coinbase-addr=YOUR_SCASHX_ADDRESS
```

Solo mine using large memory pages and disable thread binding:
```
./minerd -o 127.0.0.1:8342 -O username:password -t 4 --coinbase-addr=YOUR_SCASHX_ADDRESS --largepages --no-affinity
```

Mine at a pool using large memory pages and print out networking and debugging messages:
```
./minerd --url=stratum+tcps://pool.domain.com:1234 --user=checkyourpool --pass=checkyourpool --largepages -P -D
```

Benchmark 5000 hashes (default is 1000), on each of 4 miner threads (default will use number of processors):
```
./minerd --benchmark --nonces 5000 -t 4
```
