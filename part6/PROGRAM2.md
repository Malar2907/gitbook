# Smart Contract Program
<h3>Step 1: Introducing two parties to smart contract</h3>
<p>Any smart contract is concluded by two sides. Since this smart contract is for a marketplace, two roles are going to be there:</p>
<p></ul>
<li>The <strong>client</strong>, who needs to get some home service done.</li>
<li>The <strong>tasker</strong>, who completes a task and gets paid for it.</li>
</ul></p>
<p>A client pays a tasker for fulfilling a task, so you should add a payment amount to the smart contract as well;  called here as <strong>payAmount</strong>.</p>

<p>Before coding these two roles and the payment amount into the smart contract, an automated unit test with JavaScript is written:</p>

```
const OddjobPayContract = artifacts.require('OddjobPayContract')

contract('OddjobPayContract', accounts => {
  const client   = accounts[1]
  const tasker   = accounts[2]

  describe('client property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.client()).to.equal(client)
    })
  })

  describe('tasker property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.tasker()).to.equal(tasker)
    })
  })

  describe('payAmount property', () => {
    it('is initialized with 0 after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      const payAmount = await deployedContract.payAmount()
      expect(payAmount.toNumber()).to.equal(0)
    })
  })
})
```
<p>Now it’s time to implement exactly the same logic to write the actual smart contract with Solidity:</p>

```
contract OddjobPayContract {
  address public client;
  address public tasker;

  uint256 public payAmount;

  constructor (address _client, address _tasker) public {
    client = _client;
    tasker = _tasker;

    payAmount = 0;
  }
}
```
<p>To find out whether  Solidity code is correct, run the test in Truffle using this code </p>

```
const OddjobPayContract = artifacts.require('OddjobPayContract')

module.exports = (deployer, _, accounts) => {
  deployer.deploy(OddjobPayContract, accounts[1], accounts[2], { from: accounts[0] })
}
```
<h3>Step 2: Enabling a client to transfer money to a smart contract</h3>
<p>A smart contract acts like a separate account that can either send money to a tasker or send it back to a client. But first, a client must be able to send money to the smart contract. At this step, you need to add this functionality to the smart contract.</p>

<p>As usual, start from updating the test file. You should specify that nobody but a client can transfer money to a smart contract and that it’s impossible for anyone to increase the payAmount:</p>

```
const OddjobPayContract = artifacts.require('OddjobPayContract')

const Web3 = require('web3')

const web3 = new Web3(
  new Web3.providers.HttpProvider(
    'http://localhost:8545'
  )
)

const getRandomItem = array => (
  array[Math.floor(Math.random() * array.length)]
)

contract('OddjobPayContract', accounts => {
  const client   = accounts[1]
  const tasker   = accounts[2]

  const unknownAccount = getRandomItem(accounts.slice(3))

  describe('client property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.client()).to.equal(client)
    })
  })

  describe('tasker property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.tasker()).to.equal(tasker)
    })
  })

  describe('payAmount property', () => {
    it('is initialized with 0 after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      const payAmount = await deployedContract.payAmount()
      expect(payAmount.toNumber()).to.equal(0)
    })
  })

  describe('payable fallback', () => {
    describe('msg.sender validation', () => {
      it('deployer can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: deployer, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('tasker can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: tasker, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('unknown account can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: unknownAccount, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('only client can increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: client, value: web3.toWei(5, 'ether')
          }).then(() => done()).catch(() => done('e'))
        })()
      })
    })

    it('increases pay amount', async () => {
      const deployedContract = await OddjobPayContract.deployed()

      const payAmountWeiBefore = await deployedContract.payAmount()
      const payAmountBefore = web3.fromWei(payAmountWeiBefore, 'ether')

      await deployedContract.sendTransaction({
        from: client, value: web3.toWei(5, 'ether')
      })

      const payAmountWeiAfter = await deployedContract.payAmount()
      const payAmountAfter = web3.fromWei(payAmountWeiAfter, 'ether')

      expect(payAmountAfter - payAmountBefore).to.equal(5)
    })
  })
})
```
<p>Now update the smart contract by adding the code that allows a client to transfer money to it:</p>

```
contract OddjobPayContract {
  address public client;
  address public tasker;

  uint256 public payAmount;

  constructor (address _client, address _tasker) public {
    client = _client;
    tasker = _tasker;

    payAmount = 0;
  }

  function () public payable {
    require(client == msg.sender);
    payAmount += msg.value;
  }
}
```
<h3>Step 3: Allowing a smart contract to transfer money to a tasker</h3>
<p>Finally, your smart contract must be able to automatically send money to a tasker as soon as a client confirms that the task has been completed. To implement this functionality, we need to introduce a new role in the smart contract – a <strong>deployer</strong>, which is a web application on blockchain marketplace – and specify that only a deployer can initiate a transfer of money to a tasker. Also, make sure that the payAmount gets nullified once it has been sent to a tasker.</p>

<p>Let’s first implement all this logic in tests; you have to make it impossible for a deployer or any other third party to trigger a payAmount transfer from the smart contract to a tasker. At this step, you should get a long and detailed test file looking something like this:</p>

```
const OddjobPayContract = artifacts.require('OddjobPayContract')

const Web3 = require('web3')

const web3 = new Web3(
  new Web3.providers.HttpProvider(
    'http://localhost:8545'
  )
)

const getRandomItem = array => (
  array[Math.floor(Math.random() * array.length)]
)

contract('OddjobPayContract', accounts => {
  const deployer = accounts[0]
  const client   = accounts[1]
  const tasker   = accounts[2]

  const unknownAccount = getRandomItem(accounts.slice(3))

  describe('deployer property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.deployer()).to.equal(deployer)
    })
  })

  describe('client property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.client()).to.equal(client)
    })
  })

  describe('tasker property', () => {
    it('is initialized after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      expect(await deployedContract.tasker()).to.equal(tasker)
    })
  })

  describe('payAmount property', () => {
    it('is initialized with 0 after deployment', async () => {
      const deployedContract = await OddjobPayContract.deployed()
      const payAmount = await deployedContract.payAmount()
      expect(payAmount.toNumber()).to.equal(0)
    })
  })

  describe('payable fallback', () => {
    describe('msg.sender validation', () => {
      it('deployer can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: deployer, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('tasker can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: tasker, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('unknown account can not increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: unknownAccount, value: web3.toWei(5, 'ether')
          }).then(() => done('e')).catch(() => done())
        })()
      })

      it('only client can increase pay amount', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendTransaction({
            from: client, value: web3.toWei(5, 'ether')
          }).then(() => done()).catch(() => done('e'))
        })()
      })
    })

    it('increases pay amount', async () => {
      const deployedContract = await OddjobPayContract.deployed()

      const payAmountWeiBefore = await deployedContract.payAmount()
      const payAmountBefore = web3.fromWei(payAmountWeiBefore, 'ether')

      await deployedContract.sendTransaction({
        from: client, value: web3.toWei(5, 'ether')
      })

      const payAmountWeiAfter = await deployedContract.payAmount()
      const payAmountAfter = web3.fromWei(payAmountWeiAfter, 'ether')

      expect(payAmountAfter - payAmountBefore).to.equal(5)
    })
  })

  describe('sendPayAmountToTasker action', () => {
    describe('msg.sender validation', () => {
      it('can be triggered by deployer', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendPayAmountToTasker({ from: deployer })
            .then(() => done())
            .catch(() => done('e'))
        })()
      })

      it('can not be triggered by client', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendPayAmountToTasker({ from: client })
            .then(() => done('e'))
            .catch(() => done())
        })()
      })

      it('can not be triggered by tasker', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendPayAmountToTasker({ from: tasker })
            .then(() => done('e'))
            .catch(() => done())
        })()
      })

      it('can not be triggered by unknown account', done => {
        (async () => {
          const deployedContract = await OddjobPayContract.deployed()

          deployedContract.sendPayAmountToTasker({ from: unknownAccount })
            .then(() => done('e'))
            .catch(() => done())
        })()
      })
    })

    it('nullifies pay amount', async () => {
      const deployedContract = await OddjobPayContract.new(
        client, tasker, { from: deployer }
      )

      await deployedContract.sendTransaction({
        from: client, value: web3.toWei(5, 'ether')
      })

      const payAmountWeiBefore = await deployedContract.payAmount()
      const payAmountBefore = web3.fromWei(payAmountWeiBefore, 'ether')

      await deployedContract.sendPayAmountToTasker({ from: deployer })

      const payAmountWeiAfter = await deployedContract.payAmount()
      const payAmountAfter = web3.fromWei(payAmountWeiAfter, 'ether')

      expect(payAmountAfter - payAmountBefore).to.equal(-5)
      expect(payAmountAfter.toNumber()).to.equal(0)
    })
  })
})
```
<p>Now add this logic to the smart contract itself: introduce a deployer and allow it to transfer money to a tasker. The full smart contract looks like this:</p>

```
contract OddjobPayContract {
  address public deployer;

  address public client;
  address public tasker;

  uint256 public payAmount;

  constructor (address _client, address _tasker) public {
    deployer = msg.sender;

    client = _client;
    tasker = _tasker;

    payAmount = 0;
  }

  function () public payable {
    require(client == msg.sender);
    payAmount += msg.value;
  }

  function sendPayAmountToTasker() public {
    require(deployer == msg.sender);

    // transfer pay amount to tasker
    tasker.transfer(payAmount);

    // nullify pay amount manually
    payAmount = 0;
  }
}
```
<h3>Run a deployment script</h3>
```
const Web3 = require('web3')
const contract = require('truffle-contract')
const SmartContract = contract(require('./build/contracts/OddjobPayContract'))

const deployer = '0x2Af1552C6b02a1C8eE6B8A9175867198EE7DeCA1'
const client   = '0x4baB642a718738504871e4e9399C8F103aE0A23E'
const tasker   = '0xAcF1006EA19f9ed5b276204036010687C1A79683'

const web3Provider = new Web3.providers.HttpProvider(
  'http://10.10.11.75:8545' // address of the Parity node
)

const web3 = new Web3(web3Provider)

web3.eth.getAccounts(error => {
  if (error) {
    throw new Error(`
      Error while fetching accounts from RPC!
      Check RPC address! The problem may be there!
    `)
  }
})

const fetchBalanceByAddress = async address => {
  return new Promise(resolve => {
    web3.eth.getBalance(address, (_, balance) => {
      resolve(web3.fromWei(balance, 'ether').toString())
    })
  })
}

const printBalancesToConsole = async () => {
  const deployerBalance = await fetchBalanceByAddress(deployer)
  const clientBalance   = await fetchBalanceByAddress(client)
  const taskerBalance   = await fetchBalanceByAddress(tasker)

  console.log(`Deployer: ${deployerBalance}; Client: ${clientBalance}; Tasker: ${taskerBalance}`)
}

const run = async () => {
  await printBalancesToConsole()

  SmartContract.setProvider(web3Provider)

  const smartContract = await SmartContract.new(
    client, tasker, { gas: 1000000, from: deployer }
  )

  await printBalancesToConsole()

  await smartContract.sendTransaction({ from: client, value: web3.toWei(0.1, 'ether') })

  await printBalancesToConsole()

  await smartContract.sendPayAmountToTasker({ from: deployer })

  await printBalancesToConsole()
}

run()
```
<h3>Execute your smart contract</h3>
<p>Once your smart contract is deployed, you can execute it. <p>
