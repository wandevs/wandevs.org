---
layout: page
author: tyrion70
title: Create a Wanchain Account
---
This document describes various ways to create a Wanchain account. Remember to always make 
a copy of your keystore files or seedphrase and use responsible passwords. The best way to
create an account is to use a hardware wallet like Trezor or Ledger.

## Wanwalletgui
In WanWalletGui click on new account in the menu
![Create Account](/img/create-new-account.png)

When prompted enter the account name and the password you want to use.
![Enter passphrase](/img/enter-passphrase.png)

## Console
See [Using the Console](/docs/using-the-console) to start the console. In the console type:
```
> personal.newAccount()
Passphrase: [type your passphrase]
Repeat passphrase: [type your passphrase again]
"0xca3ae232892d94f48fa76946a1a04dd2a972f629" << This is your address
>
```
A keystore file should be saved in your local keystore directory. 
The keystore directory is located inside your datadir

## WanMask
To install the WanMask browser extension go to the [WanMask.io](https://wanmask.io) website.

Follow the steps provided by WanMask to create an account. 

## MyWanWallet
In MyWanWallet simply click the "New Wallet" tab and enter a passphrase. This is not a recommended 
way to create an account because there are many ways you can be made a victim of hacks when you do it 
like that. Preferably download an offline release of MyWanWallet or use a hardware wallet instead.
