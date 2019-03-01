---
layout: page
title: Overview of Privacy Transactions
---

Privacy transactions on Wanchain are a way for a user to send WAN to another
user without specifying who is the recipient. With a privacy transaction the
world can see that a user made a transaction, and upon a deeper inspection of
the transaction can see how much WAN was sent in the transaction, but the world
cannot see who the recipient will be. The world, however, may be able to make
an educated guess at who is the recipient, depending on some factors that we’ll
discuss below.

Privacy transactions are carried out on Wanchain by way of a smart contract
built into the protocol. The smart contract resides at the following hard-coded
address, and has the following ABI.

**Privacy Contract Address**
```
0x0000000000000000000000000000000000000064
```

**Privacy Contract ABI**
```
[{"constant":false,"type":"function","stateMutability":"nonpayable","inputs":[{"name":"OtaAddr","type":"string"},{"name":"Value","type":"uint256"}],"name":"buyCoinNote","outputs":[{"name":"OtaAddr","type":"string"},{"name":"Value","type":"uint256"}]},{"constant":false,"type":"function","inputs":[{"name":"RingSignedData","type":"string"},{"name":"Value","type":"uint256"}],"name":"refundCoin","outputs":[{"name":"RingSignedData","type":"string"},{"name":"Value","type":"uint256"}]},{"constant":false,"inputs":[],"name":"getCoins","outputs":[{"name":"Value","type":"uint256"}]}]
```

### Transaction Workflow

Unlike a regular transaction, which involves a single transaction on the
blockchain that transfers the account balance, a privacy transaction consists
of two underlying transactions: a lock (`buyCoinNote`) transaction made by the
sender, which locks the WAN with the privacy smart contract, and a redeem
(`refundCoin`) transaction made by the recipient, which transfers the WAN from
the smart contract to the recipient.

![Privacy Transaction](/img/privacy_transaction.png)

When a user locks WAN in the privacy smart contract, it is not possible to see
who the intended recipient is. This is because the lock transaction does not
contain the recipient’s address within the transaction call data, but instead
only contains ring signature data from the recipient’s one-time address. Thus,
while we can see transactions that lock funds into the privacy contract, and
other transactions that redeem funds from the privacy contract, we cannot link
any lock and redeem transactions by anything other than value.

### Allowed Transaction Amounts

Hypothetically, if a user were to lock 427 WAN into the privacy smart contract,
and then soon after another user were to redeem 427 WAN from the contract, it
would be fairly obvious that the two transactions were linked. Transaction
values accordingly can reveal connections between senders and recipients. For
this reason, the privacy contract was made to only allow certain values to be
locked for a given privacy transaction.

Allowed amounts:
- 10 WAN
- 20
- 50
- 100
- 200
- 500
- 1000
- 5000
- 50000

When locking funds into the privacy contract, a user can thus only lock an
amount in the list above. Likewise, a user cannot redeem partial amounts, but
must redeem the full amount that was locked to the one-time address.
