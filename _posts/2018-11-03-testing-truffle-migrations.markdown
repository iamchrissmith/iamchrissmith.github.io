---
layout: post
title:  "Testing Truffle Migrations"
date:   2018-11-04 16:50:00
description: Some thoughts on how tests can be used to develop better migrations in Truffle.
categories: [
  blog,
  Solidity,
  Smart Contract,
  Ethereum,
  Testing,
  Truffle,
  Migrations]
---

At the beginning of October, I spoke at TrufflCon 2018 on "Smart Contract Testing Strategies".  [You can view the code and presentation from that talk on Github](https://github.com/iamchrissmith/trufflecon-2018-testing-strategies){:target=>"blank"}. While preparing for that talk, I looked at a lot of smart contract tutorials that showed smart contract tests using `MyContract.deployed()` in their mocha tests.  While this benefits your tests by making use of the Truffle [Clean Room Environment](https://truffleframework.com/docs/truffle/testing/testing-your-contracts#clean-room-environment){:target=>"blank"} and speeds up your tests by only deploying a new set of contracts once per test file, I do not believe this leads to clean, easy to understand unit tests for your smart contracts.  As I describe in that talk, I believe it is better to use `beforeEach` blocks in your tests to deploy new contracts for each test, creating a clean state in which each individual test can run.  For instance:
```javascript
const MyContractAbstraction = artifacts.require('MyContract');

contract('My Contract', function(accounts) {
  const from = accounts[0];
  let myContract;

  beforeEach(async () => {
    myContract = await MyContractAbstraction.new({from});
  });

  describe('.transfer', () => {
    it('test that transfer works', async () => {
      // some tests that start with a fresh MyContract contract instance
    });
    it('test that transfer works again', async () => {
      // the balance of this test would be unaffected by the first test
    });
  });
});
```

After presenting my talk and thinking about the different uses of Truffle migrations during testing (i.e. they can be very helpful for writing integration tests where you want state to persist between test statements), I realized there was an important type of test that these migrations do help with and that I hadn't read about or used before.

## Testing Migrations with Truffle

Truffle migrations serve two purposes: 
1. They setup the clean room environment for your `truffle test`ing. 
2. More importantly, if you want to use Truffle to manage your singleton contracts on test- and mainnets, they are how you deploy and potentially upgrade contracts.

It is this second use case that led me to think about testing truffle migrations.  In most simple Dapp tutorials you only need to launch one contract.  However, even if it is an ERC-20 token contract, it could be important how it is configured.  If you didn't hard code your `decimalUnits`, `totalSupply`, and `symbol` into your contract, you will have to set those via your constructor when making the new contract call.  Lets say you have this contract:

```javascript
pragma solidity 0.4.24;

import "openzeppelin-solidity/contracts/token/ERC20/StandardToken.sol";

contract SimpleToken is StandardToken {
  constructor(
        uint256 _initialAmount,
        string _name,
        uint8 _decimalUnits,
        string _symbol
    )
        public
    {
      name = _name;
      decimals = _decimalUnits;
      symbol = _symbol;
      totalSupply_ = _initialAmount;
      balances[msg.sender] = _initialAmount;
      emit Transfer(address(0), msg.sender, totalSupply_);
    }
}
```

This will launch a very basic ERC-20 token that can be setup dynamically. So you might write a migration like this: `/migrations/2_deploy_token.js`

```javascript
const SimpleToken = artifacts.require('SimpleToken');

module.exports = function(deployer, network, accounts) {
  deployer.deploy(
    SimpleToken,
    1000000000000000000000000000, // 1 billion tokens initial supply
    'Simple Token',
    18,
    'STK',
  );
};
```

Great, you now have a very simple ERC-20 and a migration to launch it.  You can launch ganache locally, run `truffle migrate` and start interacting with your new token on your local testnet.

However, once your ready to go to Rinkeby, or even Mainnet, you're trusting that that migration was setup correctly.  If you forgot to convert your token's initial supply based on decimal units, you might launch a token with `100` as the initial supply and have an impossibly small supply of tokens.  What if you change your token contract to improve it and have to introduce a new constructor parameter?  This will break your migration and you might not discover it until you try to launch.  These all sound like good reasons to write a test.  You want to know your code works now as expected and will continue to work in the future as expected.

Thankfully, Truffle gives us a really great way to test our migrations.  (Remember, `MyContract.deployed()` from above that I said I didn't like to use in my tests...) To test my Token migration here I could have a test file like:

```javascript
/* eslint max-len:0 */
const SimpleTokenAbstraction = artifacts.require('SimpleToken');

contract('deploy_SimpleToken', function(accounts) {
  const expectedContractArgs = {
    owner: accounts[0],
    initialAmount: 1000000000000000000000000000, // 1 billion tokens initial supply
    name: 'Simple Token',
    decimalUnits: 18,
    symbol: 'STK',
  };

  before( async () => {
    this.deployed = await SimpleTokenAbstraction.deployed();
  });
```
Then I could write a test that made sure the contract got deployed:

```javascript

  describe('Deployed', () => {
    it('it should deploy the SimpleToken contract to the network', async () => {
      assert.isNotNull(this.deployed, 'The contract was not deployed');
    });
  });
```
And ones to make sure it was deployed with the correct parameters, like:
```javascript
  describe('constructor', () => {
    it('it was deployed and assigned the correct balance', async () => {
      const errMsg = 'Owner did not receive the initialAmount';
      const ownerBalance = await this.deployed.balanceOf.call(expectedContractArgs.owner);
      assert.equal(ownerBalance.toNumber(), expectedContractArgs.initialAmount, errMsg);
    });
  })
});
```

This allows me to ensure my migrations, a critical part of my application, will function as expected.

In this simple example it may not seem that important, but as you get into more complicated smart contracts and interactions between smart contracts, knowing that your migrations are going to deploy your system correctly, can be incredibly important.

One such example I have run into is with upgradeable smart contracts. When the smart contract that holds the functionality gets launched it needs to be initialized.  We can't use constructors with implementation contracts because the proxies we launch later won't get the constructor state run.  However, in order to ensure the continued and expected functionality of that implementation, it needs to be initialized.  This is a great example where a simple oversight could lead to disasterous consequences... say if you didn't initialized and therefore the owner of your implementation is never set... (for more details about how important this is see [CoinTelegraph's the Parity Multisig Hack post mortem from Nov 2017](https://cointelegraph.com/news/parity-multisig-wallet-hacked-or-how-come){:target=>"blank"}).

I'm hoping to write more about upgradeable contracts, implementations and initialization in the near future, but in the meantime if you want to learn more about those topics, check out this great blog article from [Zeppelin and Elena Nadolinski](https://blog.zeppelinos.org/proxy-patterns/){:target=>"blank"}.
