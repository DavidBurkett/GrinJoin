# GrinJoin
A CoinJoin service for Grin

### Background
In 2013, Greg Maxwell proposed[1] the CoinJoin protocol as a way of anonymizing transactions. CoinJoin is a way of combining the inputs and outputs from multiple transactions into a single transaction. Doing such erased the original transaction boundaries, making it infeasible for blockchain analysts to determine which outputs spent which inputs.

Three years later, the original Mimblewimble protocol was outlined[2] by the pseudonymous Tom Elvis Jedusor. This combined CoinJoin with Confidential Transactions to make a non-interactive protocol for performing CoinJoins, which became the basis for the Grin cryptocurrency.

In Grin, every block is simply a CoinJoin of all of the other transactions in the block, resulting in one big transaction. This gives Grin an enormous privacy advantage over bitcoin, breaking transaction linkability! *In theory*. 

### Problem

There are a few problems though. Privacy is only gained if there are other transactions to join yours with. Since Grin is still new, there are only a few transactions per minute on average, meaning there aren't very many other transactions to mix yours with. To make matters worse, anyone running a node that wants to monitor the transaction pool can see nearly all of the individual transactions before they've been combined in a block. So much for unlinkability!

### Proposed Soution

As usage grows, these issues become less of a problem. In the meantime, there are ways to make the situation significantly better for those who are willing to wait a few blocks before including their transactions. By introducing a *trust-minimized* central server (or group of servers), transactions can be collected and combined for a period of time before ever being broadcast to the network and included in a block. Now only the central server knows which inputs and outputs belong to you!

But now this central service knows which node a transaction came from. How can we solve this problem? The same way we solve it for Grin transactions: Dandelion (and later, i2p). By introducing a new tx pool similar to Dandelion's stempool, let's call it the JoinPool, we can route our transactions through other nodes at random, obscuring their origin. Rather than "fluffing"/broadcasting a transaction after X hops (on average), the transactions in the JoinPool can instead be sent to the CoinJoin server to be joined with other transactions.

So now only the central server knows which outputs spend which inputs, but has no idea where that transaction came from. If we wanted, we could even setup a heirarchy of servers, so there's not a single server that knows all of the original transactions. Each node could choose either a specific trusted server or a random one to send their transactions to. These secondary servers can aggregate transactions as they receive them, and after aggregating "enough" transactions, then send the final, joined transaction to the primary CoinJoin server to be joined with the aggregate transactions from the other secondary services.

### Roadmap

1. Finish implementing support for a primary "GrinJoin" server, and start hosting it. Interested parties can manually submit transactions to be included in the next GrinJoin transaction to test the service. I will initially log transaction info for debugging purposes, so at this stage, assume I could compromise your privacy if I wanted to.
2. Implement a JoinPool in Grin++ to obscure the origin of each transaction. Transactions that choose to use this new CoinJoin feature will eventually make their way to the GrinJoin server. I will remove all logging once everything is stable.
3. Add support to GrinJoin for running a 'secondary' server, and add the ability to Grin++ to choose which server to submit your transactions to.


[1] https://bitcointalk.org/index.php?topic=279249
[2] https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.txt
