---
layout: post
title:  "Testing Truffle Migrations"
date:   2018-11-09 16:50:00
description: Some thoughts on how tests can be used to develop better migrations in Truffle.
categories:
- blog
- Solidity
- Smart Contract
- Ethereum
- Testing
- Truffle
- Migrations
---

At the beginning of October, I spoke at TrufflCon 2018 on "Smart Contract Testing Strategies".  [You can view the code and presentation from that talk on Github](https://github.com/iamchrissmith/trufflecon-2018-testing-strategies){:target=>"blank"}. During that preparing for that talk, I looked at a lot of smart contract tutorials that showed smart contracts tests using `MyContract.deployed()` in their mocha tests.  While this benefits, your tests by making use of the Truffle [Clean Room Environment](https://truffleframework.com/docs/truffle/testing/testing-your-contracts#clean-room-environment){:target=>"blank"} and speeds up your tests by only deploying a new set of contracts once per test file, I do not believe this leads to clean, easy to understand unit tests for your smart contracts.  As I describe in that talk, I believe it is better to use `beforeEach` blocks in your tests to deploy new contracts for each test, creating a clean state for test to run in.  For instance:
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
  });
});
```

After presenting my talk and thinking about the different uses of Truffle migrations during testing (i.e. they can be very helpful for writing integration tests where you want state to persist between test statements), I realized there was an important type of test that these migrations do help with and that I hadn't read about or used before.

## Testing Migrations with Truffle
