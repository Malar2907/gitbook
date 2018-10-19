# Smart Contract Program
<h3>Your first citizen: the greeter</h3>
<p>Now that you’ve mastered the basics of Ethereum, let’s move into your first serious contract.
The Frontier is a big open territory and sometimes you might feel lonely, so our first order of
business will be to create a little automatic companion to greet you whenever you feel lonely.
We’ll call him the “Greeter”.</p>
<p>The Greeter is an intelligent digital entity that lives on the blockchain and is able to have
conversations with anyone who interacts with it, based on its input. It might not be a talker,
but it’s a great listener. Here is its code:</p>
```
contract mortal {
/* Define variable owner of the type address*/
address owner;
/* this function is executed at initialization and sets the owner of the contract */
function mortal() { owner = msg.sender; }
/* Function to recover the funds on the contract */
function kill() { if (msg.sender == owner) suicide(owner); }
}
contract greeter is mortal {
/* define variable greeting of the type string */
string greeting;
/* this runs when the contract is executed */
function greeter(string _greeting) public {
greeting = _greeting;
}
/* main function */
function greet() constant returns (string) {
return greeting;
}
}
```
<p>You'll notice that there are two different contracts in this code: "mortal" and "greeter". This is
because Solidity (the high level contract language we are using) has inheritance, meaning
that one contract can inherit characteristics of another. This is very useful to simplify coding
as common traits of contracts don't need to be rewritten every time, and all contracts can be
written in smaller, more readable chunks. So by just declaring that greeter is mortal you
inherited all characteristics from the "mortal" contract and kept the greeter code simple and
easy to read.</p>
<p>The inherited characteristic "mortal" simply means that the greeter contract can be killed by
its owner, to clean up the blockchain and recover funds locked into it when the contract is no
longer needed. Contracts in ethereum are, by default, immortal and have no owner, meaning
that once deployed the author has no special privileges anymore. Consider this before
deploying.</p>
<h3>Installing a compiler</h3>
<p>Before you are able to Deploy it though, you'll need two things: the compiled code, and the
Application Binary Interface, which is a sort of reference template that defines how to
interact with the contract.</p>
<p>The first you can get by using a compiler. You should have a solidity compiler built in on your
geth console. To test it, use this command:</p>
```
eth.getCompilers()
```
If you have it installed, it should output something like this:
```
['Solidity' ]
```
If instead the command returns an error, then you need to install it.

<h3>Using an online compiler</h3>
If you don't have solC installed, we have a online solidity compiler available. But be aware
that <strong>if the compiler is compromised, your contract is not safe.</strong> For this reason, if you
want to use the online compiler we encourage you to host your own.
```
Install SolC on Ubuntu
Press control+c to exit the console (or type exit) and go back to the command line. Open the
terminal and execute these commands:
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install solc
which solc
```
Take note of the path given by the last line, you'll need it soon
<h3>Install SolC on Mac OSX</h3>
You need brew in order to install on your mac
```
brew install cpp-ethereum
brew linkapps cpp-ethereum
which solc
```
Take note of the path given by the last line, you'll need it soon.
<h3>Install SolC on Windows</h3>
You need chocolatey in order to install solc.
```
cinst -pre solC-stable
```
Windows is more complicated than that, you'll need to wait a bit more.
<p>If you have the SolC Solidity Compiler installed, you need now reformat by removing spaces
so it fits into a string variable</p>
<h3>Compile from source</h3>
```
git clone https://github.com/ethereum/cpp-ethereum.git
mkdir cpp-ethereum/build
cd cpp-ethereum/build
cmake -DJSONRPC=OFF -DMINER=OFF -DETHKEY=OFF -DSERPENT=OFF -DGUI=OFF -DTESTS=OFF -DJSCONSOLE=OFF ..
make -j4
make install
which solc
Linking yo
```
<h3>Linking your compiler in Geth</h3>
Now go back to the console and type this command to install solC, replacing path/to/solc to
the path that you got on the last command you did:
```
admin.setSolc("path/to/solc")
```
Now type again:
```
eth.getCompilers()
```
If you now have solC installed, then congratulations, you can keep reading. If you don't, then
go to our forums or subreddit and berate us on failing to make the process easier.

<h3>Compiling your contract</h3>
If you have the compiler installed, you need now reformat your contract by removing linebreaks
so it fits into a string variable (there are some online tools that will do this):
```
var greeterSource = 'contract mortal { address owner; function mortal() { owner = msg.sender; } function kill() { if (msg.sender == owner) suicide(owner); } } contract greeter is mortal { string greeting; function greeter(string _greeting) public { greeting = _greeting; } function greet() constant returns (string) { return greeting; } }'
var greeterCompiled = web3.eth.compile.solidity(greeterSource)
You have now compiled your code. Now you need to get it ready for deployment, this
includes setting some variables up, like what is your greeting. Edit the first line below to
something more interesting than 'Hello World!" and execute these commands:
var _greeting = "Hello World!"
var greeterContract = web3.eth.contract(greeterCompiled.greeter.info.abiDefinition);
var greeter = greeterContract.new(_greeting,{from:web3.eth.accounts[0], data: greeterCompiled.greeter.code, gas: 1000000}, function(e, contract){
if(!e) {
if(!contract.address) {
console.log("Contract transaction send: TransactionHash: " + contract.transactionHash + " waiting to be mined...");
} else {
console.log("Contract mined! Address: " + contract.address);
console.log(contract);
}
}
})
```
<h3>Using the online compiler</h3>
If you don't have solC installed, you can simply use the online compiler. Copy the source
code above to the online solidity compiler and then your compiled code should appear on
the left pane. Copy the code on the box labeled Geth deploy to a text file. Now change the
first line to your greeting:
```
var _greeting = "Hello World!
```
Now you can paste the resulting text on your geth window. Wait up to thirty seconds and
you'll see a message like this:
```
Contract mined! address: 0xdaa24d02bad7e9d6a80106db164bad9399a0423e
```
You will probably be asked for the password you picked in the beginning, because you need
to pay for the gas costs to deploying your contract. This contract is estimated to need 172
thousand gas to deploy (according to the online solidity compiler), at the time of writing, gas
on the test net is priced at 1 to 10 microethers per unit of gas (nicknamed "szabo" = 1
followed by 12 zeroes in wei). To know the latest price in ether all you can see the latest gas
prices at the network stats page and multiply both terms.
<p><strong>Notice that the cost is not paid to the ethereum developers, instead it goes to the
Miners, people who are running computers who keep the network running. Gas price
is set by the market of the current supply and demand of computation. If the gas
prices are too high, you can be a miner and lower your asking price.<strong></p>
<p>After less than a minute, you should have a log with the contract address, this means you've
sucessfully deployed your contract. You can verify the deployed code (compiled) by using
this command:</p>
```
eth.getCode(greeter.address)
```
If it returns anything other than "0x" then congratulations! Your little Greeter is live! If the
contract is created again (by performing another eth.sendTransaction), it will be published to
a new address.

<h3>Run the Greeter</h3>
In order to call your bot, just type the following command in your terminal:
```
greeter.greet();
```
Since this call changes nothing on the blockchain, it returns instantly and without any gas
cost. You should see it return your greeting:
```
'Hello World!'
```

