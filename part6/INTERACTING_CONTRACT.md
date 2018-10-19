# Interacting with contracts

`eth.contract` can be used to define a contract class that will comply with the contract
interface as described in its ABI definition.
```
var Multiply7 = eth.contract(contract.info.abiDefinition);
var myMultiply7 = Multiply7.at(address);
```
Now all the function calls specified in the abi are made available on the contract instance.
You can just call those methods on the contract instance and chain `sendTransaction(3,
{from: address})` or `call(3)` to it. The difference between the two is that call performs a
"dry run" locally, on your computer, while `sendTransaction` would actually submit your
transaction for inclusion in the block chain and the results of its execution will eventually
become part of the global consensus. In other words, use `call` , if you are interested only in
the return value and use `sendTransaction` if you only care about "side effects" on the state
of the contract.
In the example above, there are no side effects, therefore `sendTransaction` only burns gas
and increases the entropy of the universe. All "useful" functionality is exposed by `call` :
```
myMultiply7.multiply.call(6)
42
```
Now suppose this contract is not yours, and you would like documentation or look at the
source code. This is made possible by making available the contract info bundle and register
it in the blockchain. The `admin` API provides convenience methods to fetch this bundle for
any contract that chose to register. To see how it works, read about Contract Metadata or
read the contract info deployment section of this document.
```
// get the contract info for contract address to do manual verification
var info = admin.getContractInfo(address) // lookup, fetch, decode
var source = info.source;
var abiDef = info.abiDefinition
```