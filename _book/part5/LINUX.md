# Linux 
<h3>Debian/Ubuntu</h3>
<h3>Installing from PPA</h3>
For the latest development snapshot, both `ppa:ethereum/ethereum` and
`ppa:ethereum/ethereum-dev` are needed. If you want the stable version from the last PoC
release, add only the first one.

```
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo add-apt-repository -y ppa:ethereum/ethereum-dev
sudo apt-get update
sudo apt-get install ethereum
```
After installing, run `geth account new` to create an account on your node.
You should now be able to run `geth` and connect to the network.
Make sure to check the different options and commands with `geth --help`
You can alternatively install only the `geth` CLI with `apt-get install geth` if you don't want
to install the other utilities ( `bootnode` , `evm` , `disasm` , `rlpdump` , `ethtest` ).

<h3>Building from source</h3>
<h5>Building Geth (command line client)</h5>
Clone the repository to a directory of your choosing:
```
git clone https://github.com/ethereum/go-ethereum
```
Building geth requires some external libraries to be installed:
```
sudo apt-get install -y build-essential libgmp3-dev golang
```
Finally, build the geth program using the following command.
```
cd go-ethereum
make geth
```
You can now run build/bin/geth to start your node.
<h3>Arch</h3>
<h3>Installing from source</h3>
Install dependencies
```
pacman -S git go gcc gmp
```
Download and build geth
```
git clone https://github.com/ethereum/go-ethereum
cd go-ethereum
make geth
```
