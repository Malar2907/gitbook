# Writing a contract
<p>Contracts live on the blockchain in an Ethereum-specific binary format (Ethereum Virtual
Machine (=EVM) bytecode). However, contracts are typically written in some high level
language such as solidity and then compiled into byte code to be uploaded on the
blockchain.</p>
<p>Note that other languages also exist, notably serpent and LLL. Legacy Mutan (an early c-like
language) is no longer officially maintained.</p>

<h3>Compiling a contract</h3>
<p>Contracts live on the blockchain in an Ethereum-specific binary format (Ethereum Virtual
Machine (=EVM) bytecode). However, contracts are typically written in some high level
language such as solidity and then compiled into byte code to be uploaded on the
blockchain.</p>
For the frontier release, geth supports solidity compilation through system call to `solc` ,
the command line solidity compiler by Christian R. and Lefteris K. You can try Solidity
realtime compiler (by Christian R) or Cosmo or Mix.
If you start up your geth node, you can check if the solidity compiler is available. This is
what happens, if it is not:
```
> eth.compile.solidity("")
eth_compileSolidity method not available: solc (solidity compiler) not found
at InvalidResponse (<anonymous>:-57465:-25)
at send (<anonymous>:-115373:-25)
at solidity (<anonymous>:-104109:-25)
at <anonymous>:1:1
```
After you found a way to install `solc` , you make sure it's in the path. If `eth.getCompilers()`
still does not find it (returns an empty array), you can set a custom path to the `solc`
executable on the command line using th `solc` flag.
```
geth --datadir ~/frontier/00 --solc /usr/local/bin/solc --natspec
```
You can also set this option at runtime via the console:
```
> admin.setSolc("/usr/local/bin/solc")
solc v0.9.32
Solidity Compiler: /usr/local/bin/solc
Christian <c@ethdev.com> and Lefteris <lefteris@ethdev.com> (c) 2014-2015
true
```
Let us take this simple contract source:
```
> source = "contract test { function multiply(uint a) returns(uint d) { return a * 7; } }"
```
This contract offers a unary method: called with a positive integer `a` , it returns `a * 7` .<br>
You are ready to compile solidity code in the `geth` JS console using `eth.compile.solidity` :
```
> contract = eth.compile.solidity(source).test
{
code: '605280600c6000396000f3006000357c010000000000000000000000000000000000000000000000000000000090048063c6888fa114602e57005b60376004356041565b8060005260206000f35b6000600782029050604d565b91905056'
info: {
language: 'Solidity',
languageVersion: '0',
compilerVersion: '0.9.13',
abiDefinition: [{
constant: false,
inputs: [{
name: 'a',
type: 'uint256'
} ],
name: 'multiply',
outputs: [{
name: 'd',
type: 'uint256'
} ],
type: 'function'
} ],
userDoc: {
methods: {
}
},
developerDoc: {
methods: {
}
},
source: 'contract test { function multiply(uint a) returns(uint d) { return a * 7; } }'
}
}
```

The compiler is also available via RPC and therefore via web3.js to any in-browser Ðapp
connecting to `geth` via RPC/IPC.

The following example shows how you interface `geth` via JSON-RPC to use the compiler.
```
./geth --datadir ~/eth/ --loglevel 6 --logtostderr=true --rpc --rpcport 8100 --rpccorsdomain '*' --mine console 2>> ~/eth/eth.log
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compileSolidity","params":["contract test { function multiply(uint a) returns(uint d) { return a * 7; } }"],"id":1}' http://127.0.0.1:8100
```
The compiler output for one source will give you contract objects each representing a single
contract. The actual return value of `eth.compile.solidity` is a map of contract name --
contract object pairs. Since our contract's name is `test` ,
`eth.compile.solidity(source).test` will give you the contract object for the test contract
containing the following fields:<br>

<ul>
<li><strong>code</strong> : the compiled EVM code</li>
<li><strong>info<strong> : the rest of the metainfo the compiler outputs</li>
<ul><li><strong>source</strong>: the source code</li>
<li><strong>language</strong> : contract language (Solidity, Serpent, LLL)</li>
<li><strong>languageVersion</strong> : contract language version</li>
<li><strong>compilerVersion</strong> : compiler version</li>
<li><strong>abiDefinition</strong> : Application Binary Interface Definition</li>
<li><strong>userDoc</strong> : NatSpec user Doc</li>
<li><strong>developerDoc</strong> : NatSpec developer Doc</li></ol>
</ul>


<p>The immediate structuring of the compiler output (into code and info ) reflects the two
very different <strong>paths of deployment</strong>. The compiled EVM code is sent off to the blockchain
with a contract creation transaction while the rest (info) will ideally live on the decentralised
cloud as publicly verifiable metadata complementing the code on the blockchain.</p>
<p>If your source contains multiple contracts, the output will contain an entry for each contact,
the corresponding contract info object can be retrieved with the name of the contract as
attribute name. You can try this by inspecting the most current GlobalRegistrar code:</p>
```
contracts = eth.compile.solidity(globalRegistrarSrc)
```

