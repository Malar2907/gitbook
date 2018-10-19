# Creating and Deploying contract
Now that you got both an unlocked account as well as some funds, you can create a
contract on the blockchain by sending a transaction to the empty address with the evm code
as data. 
```
primaryAddress = eth.accounts[0]
MyContract = eth.contract(abi);
contact = MyContract.new(arg1, arg2, ...,{from: primaryAddress, data: evmCode})
```
All binary data is serialised in hexadecimal form. Hex strings always have a hex prefix `0x` .
Note that `arg1, arg2, ...` are the arguments for the contract constructor, in case it accepts
any.
<p>Also note that this step requires you to pay for execution. Your balance on the account (that
you put as sender in the from field) will be reduced according to the gas rules of the VM
once your transaction makes it into a block. More on that later. After some time, your
transaction should appear included in a block confirming that the state it brought about is a
consensus. Your contract now lives on the blockchain.</p>
<p>The asynchronous way of doing the same looks like this:</p>
```
MyContract.new([arg1, arg2, ...,]{from: primaryAccount, data: evmCode}, function(err, contract
if (!err && contract.address)
console.log(contract.address);
});
```