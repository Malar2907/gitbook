# Transfer units
Assuming the account you are using as sender has sufficient funds, sending ether couldn't
be easier. Which is also why you should probably be careful with this! You have been
warned.
```
eth.sendTransaction({from: '0x036a03fc47084741f83938296a1c8ef67f6e34fa', to: '0xa8ade7feab1ece71446bed25fa0cf6745c19c3d5'
```
Note the unit conversion in the `value` field. Transaction values are expressed in weis, the
most granular units of value. If you want to use some other unit (like `ether` in the example
above), use the function `web3.toWei` for conversion.<br><br>
Also, be advised that the amount debited from the source account will be slightly larger than
that credited to the target account, which is what has been specified. The difference is a
small transaction fee, discussed in more detail later.<br><br>
Contracts can receive transfers just like externally controlled accounts, but they can also
receive more complicated transactions that actually run (parts of) their code and update their
state. In order to understand those transactions, a rudimentary understanding of contracts is
required.
