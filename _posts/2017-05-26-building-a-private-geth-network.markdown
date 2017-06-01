---
layout: post
title:  "Up and Running with a Private Go-Ethereum Network"
date:   2017-05-26 08:33:00
description: The first of several ethereum, Go-Ethereum and solidity posts.
categories:
- blog
- ethereum
- blockchain
- Go-Ethereum
- geth
- AWS
- Amazon Web Services
---

## Setting Up AWS
We are going to focus on getting this set up on AWS since that is the most flexible for a team to work on contracts in the future.  

This is not a tutorial on AWS (though if I come across Geth specific tips, I'll include them.)  Once your AWS is set up you can SSH into it using:
`ssh -i /path/my-key-pair.pem ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com` (where ec2-user is the right user for your environment and the @ address for your server).
If you get an error about the permissions for your .pem file being wrong, you should enter:
`chmod 400 /path/my-key-pair.pem`

You can also use AWS-CLI by following [these instructions](){:target="blank"}.

## Getting Geth installed

Once you have SSH'ed into your AWS machine you should be on an Ubuntu command line.  From here, you can follow the [Geth install instructions](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Ubuntu){:target="blank"}. Personally, I used the following commands:
- `sudo apt-get install software-properties-common`  
- `sudo add-apt-repository -y ppa:ethereum/ethereum`  
- `sudo apt-get update`  
- `sudo apt-get install ethereum`  

Once these are run, you will be able to run Geth, in all its glory, on your AWS.  Check that Geth is installed with `geth --help`

## Getting your first node set up
Since we are setting up our own network we do not get the benefit of the work that has gone into the live Ethereum chain or the test chains.  However, we get the benefit of being the only ones on the chain and being in complete control of it.  At this early stage for me, some of the downsides of having your own chain seem to be:
- doing more setup work
- being responsible for the security/safety/uptime/connections of your own network.

### Genesis Files
This is a `.json` file that sets up the basic configuration of your network.  It handles things like giving an initial account Ether (which should not be entirely necessary), setting the mining difficulty, and many other items that I am not 100% what they do.  Here is my [genesis file](/assets/file/genesis.json){:target=>"blank"}.

You need to put the contents of that file on your AWS server.  I prefer to place the files for ethereum in a directory, so here is how I get the file up there.

- `mkdir ethchain`  
- `cd ethchain`  
- `nano genesis.json`  
- This last command will open a text editor in your terminal.  Paste in the genesis files.  
- You can save the file (write out) with `CTRL+O`
- Then exit with `CTRL+X`  
- You can check that your contents got saved with `cat genesis.json`.

Ok, at this point you should have everything in place to start your first node.  (If you want to read some docs about what you are about to do, you can `geth --help` or visit the [github repo](https://github.com/ethereum/go-ethereum){:target=>"blank"}.)

### Let's get that node started

We are going to need a data directory to hold stuff.  So `mkdir datadir` from within your ethchain directory. Now we need to initialize the node. Use `get --datadir ~/ethchain/datadir/ init genesis.json`.  You will probably see some warnings about accounts and etherbase.  Do not worry about these.

Now we are ready to get it started with:

```
geth --identity "[SOMETHING]" --rpc --rpcport "8545" --nodiscover --rpccorsdomain "*" --datadir ~/ethchain/datadir/ --port "30301" --networkid [PICK A NUMBER FOR YOUR NETWORK] --verbosity [1-6 number] --rpcapi "db,eth,net,web3" --nat "any" console 2>> ~/ethchain/ethchain.log
```

Lets dig into these a bit:
- `--identity`: This is the name of your node (as it will appear on the chain)
- `--rpcpost`: You can define your own and it should probably be unique between nodes.
- `--nodiscover`: This is very important as it prevents your network from being discovered by other random nodes.  
- `--port`: This should be unique between node and will need to be an open port.
- `--networkid`: This should be something unique from other networks, but will be the same among all your nodes.
- `--verbosity`: This will determine how much you see in your logs (6 is the highest).  While trying to get everything set up, I prefer 5, but after that a 4 usually is good in my limited experience.
- `2>> ~/ethchain/ethchain.log`: this pipes your logs into this file.

Once your node is started you can open a new terminal window, ssh back into your server.  Once there, you can enter `tail -F ~//.log`, this will stream your logs in this terminal window.

Assuming this is all working, you now have a Geth root node running!  Congratulations!!!

## Play around a bit

Create a new account: `personal.newAccount()`  
This will ask you for a password.  Save this and the address it returns to you someplace safe.
Check your balance:
`primary = eth.accounts[0]`  
`balance = web3.fromWei(eth.getBalance(primary), "ether");`  
Of course this will be 0 for now.  We will get into mining and connecting your second node in the next post.

## Resources

Things are moving fast in this world and I am very new to it.  I could not have put together this information without these great resources:

- [Use Go-Ethereum to Setup an Ethereum Blockchain on AWS - Part 1](https://mlgblockchain.com/setup-ethereum-on-aws-1.html){:target=>"blank"} by Michael Gord
- [Go-Ethereum Command Line Options](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options#attach)
- [Hello World Voting App](https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-2-30b3d335aa1f) by Mahesh Murthy
- [Geth Private Network](https://github.com/ethereum/go-ethereum/wiki/Setting-up-private-network-or-local-cluster)
