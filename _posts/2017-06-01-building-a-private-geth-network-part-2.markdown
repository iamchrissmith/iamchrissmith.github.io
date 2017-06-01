---
layout: post
title:  "Up and Running with a Private Go-Ethereum Network"
date:   2017-06-01 09:38:00
description: The second of several ethereum, Go-Ethereum and solidity posts about getting a test net setup.
categories:
- blog
- ethereum
- blockchain
- Go-Ethereum
- geth
- AWS
- Amazon Web Services
---

In the first post of this series, I talked about [getting AWS setup and running your genesis node](/blog/ethereum/blockchain/go-ethereum/geth/aws/amazon%20web%20services/2017/05/26/building-a-private-geth-network/) for a TestNet.  Its great to have your own node, but really, you don't want to have to SSH into the server every time you want to do something.  Ideally you would be able to connect your computer to an always live test chain and get to work.

In this short post we'll talk about how to do that.  Since we are running Ubuntu, we need a daemon that will start up the node anytime the server restarts and if the node crashes.  I found [this article about getting that setup](https://hiddentao.com/archives/2016/05/04/setting-up-geth-ethereum-node-to-run-automatically-on-ubuntu/){:target=>"blank"}.

If you are caught up with what we did in [part 1](/blog/ethereum/blockchain/go-ethereum/geth/aws/amazon%20web%20services/2017/05/26/building-a-private-geth-network/), then you should just need to follow these instructions:
1. SSH into your AWS server
2. run `sudo apt-get install supervisor`
  This installs our supervisor daemon
3. `sudo nano /etc/supervisor/conf.d/geth.conf`
  This creates a daemon config file for geth. We have to `sudo` in order to have write access to that file.
4. Inside that file enter (assuming you are using the command from [part 1](/blog/ethereum/blockchain/go-ethereum/geth/aws/amazon%20web%20services/2017/05/26/building-a-private-geth-network/)):
```
[program:geth]
command=/usr/bin/geth --identity "[SOMETHING]" --rpc --rpcport "8545" --nodiscover --rpccorsdomain "*" --datadir ~/ethchain/datadir/ --port "30301" --networkid [PICK A NUMBER FOR YOUR NETWORK] --verbosity [1-6 number] --rpcapi "db,eth,net,web3" --nat "any" console 2>> ~/ethchain/ethchain.log
autostart=true  
autorestart=true  
stderr_logfile=/var/log/supervisor/geth.err.log  
stdout_logfile=/var/log/supervisor/geth.out.log  
```
5. Now we need to restart supervisor and get the process started: `sudo supervisorctl reload`

To confirm that your node is now running you can `geth attach`.  I had to use `geth attach 127.0.0.1:8545` (see this [GitHub issue](https://github.com/ethereum/go-ethereum/issues/1908){:target=>"blank"} for more information about different `attach` commands if this doesn't work for you).

You should now be in the Geth console.  You can now leave your SSH connection to the server and your Geth node should stay running.
