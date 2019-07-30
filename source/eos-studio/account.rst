===========================================
Account Viewer
===========================================

The Account Page help you to check basic account information 
as well as perform some account operations.
Similar to the Contract Page, the Account Page is also organized
using tabs to allow multiple accounts being open at the same time.
You will also using the address bar below the tabs to enter 
the account name. EOS Studio will read the account information
and display it in the page below.


The Contract and Account pages share
the same starred account list. 你也可以用加星来标记常用的account.

Basic Information
===========================================

显示用户基本信息，包括token余额，resources (CPU/NET/RAM), 和permission keys.

When you are using a local network and the balance is always zero even
though you have issued or transfered some tokens to the account, that is
probably because you didn't setup the core symbol for the network. See here
for instructions.

Transaction History
===========================================

You can see the transaction history below the basic information.


Create a New Account
===========================================

To create a new account, click the xxx and enter the account name. You also
need to select a public key from the Keypair Manager 
to use as ``owner`` and ``active`` keys. If there is no key yet, you need
to open the Keypair Manager and create one first.
EOS Studio will check whether the account name has been used or not.
Once a new account is created, it will be starred automatically.

If you want to import a created account, just type the account name in the
address bar and `star` the account. However, you may not have the permission
to operate this account. You can go to the Keypair Manager and import its
private keys.

Tools
===========================================

EOS Studio为一些常用的账户操作提供了便捷工具。这些工具可以通过address bar右边的
按钮来访问。

- **Transfer**: make a transfer of the core symbol tokens.

- **Set the** ``eosio.code`` **permission** - xxxx actions need ``eosio.code``
  permission to run

- **Faucet (testnets only)** - click the button to claim some free tokens on a testnet

- **Buy RAMs** - buy ``100 KB`` RAMs.

