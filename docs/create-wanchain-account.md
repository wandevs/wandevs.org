---
layout: page
author: tyrion70
title: Create a Wanchain Account
---

Now that you have a Wanchain node running with RPC enabled, and that you are able to connect to the node by starting a console, you'll need to create a new Wanchain account. Without an account that has some funds, you will not be able to transact or make contract calls.

Depending on the application that you are building, you may go about the account handling in different ways; for example, you may not handle keys at all, and instead let users interact with your Dapp using [WanMask](https://wanmask.io). But for the purpose of the tutorials ahead, it will be assumed that you have an account keystore file that is either unlocked or can be retrieved by a key management package, like [keythereum](https://github.com/ethereumjs/keythereum).

## Console

The easiest way to create a new account keystore is to run the `newAccount`
command in the console (see [Using the Console](/docs/using-the-console) to
start the console).

```bash
> personal.newAccount()
Passphrase: [type your passphrase]
Repeat passphrase: [type your passphrase again]
"0xca3ae232892d94f48fa76946a1a04dd2a972f629" << This is your address
```

A keystore file should now be saved in your local keystore directory, which
will be under either the `$HOME/.wanchain/` directory or the directory
specified by the `--datadir` option used when running `gwan`.

## WanWalletGui

If you already have WanWalletGui installed, you can easily create a new
keystore file by following its account creation process. In WanWalletGui click
on `New account` in the menu.

![Create Account](/img/create-new-account.png)

When prompted enter the account name and the password you want to use.

![Enter passphrase](/img/enter-passphrase.png)

## Alternatives

### WanMask

As mentioned, it is possible to set up your Dapp so that the application never
handles any keys, but instead integrates with WanMask for transaction signing,
etc. But while WanMask will not work with the following examples, the approach
can function quite well for certain scenarios. To install the WanMask browser
extension go to the [WanMask.io](https://wanmask.io) website, and then follow
the steps provided by WanMask to create a new account.

### MyWanWallet

In MyWanWallet simply click the "New Wallet" tab and enter a passphrase.

<div class="alert alert-danger">
  This is not a recommended way to create an account because there are many
  ways you can be made a victim of hacks. Preferably download an offline
  release of MyWanWallet instead.
</div>
