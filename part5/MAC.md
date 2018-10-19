# Mac
<h3>Installing with Homebrew</h3>
By far the easiest way to install go-ethereum is to use our Homebrew tap. If you don't have
Homebrew, install it first.<br>
Then run the following commands to add the tap and install geth :
```
brew tap ethereum/ethereum
brew install ethereum
```
You can install the develop branch by running --devel :
```
brew install ethereum --devel
```
After installing, run `geth account new` to create an account on your node.
You should now be able to run `geth` and connect to the network.
Make sure to check the different options and commands with `geth --help`

<h3>Building from source</h3>
<h5>Building Geth (command line client)</h5>
Clone the repository to a directory of your choosing:
```
git clone https://github.com/ethereum/go-ethereum
```
Building geth requires some external libraries to be installed:
<ul>
<li>GMP</li>
<li>Go</li>
</ul>

```
brew install gmp go
```
Finally, build the `geth` program using the following command.
```
cd go-ethereum
make geth
```
You can now run `build/bin/geth` to start your node.