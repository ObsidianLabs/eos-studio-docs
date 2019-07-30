===========================================
Account System
===========================================

Different from Bitcoin and Ethereum, on EOSIO-based blockchains
an `account` is the basic unit to store tokens or acts as an individual 
to interact with smart contracts. Each account can have a human readable
name so it will be easy to remember.

Account有绑定的permission keys (a private key and a public key)
通过permission key来检查是否有权限操作该账户。

Account Name
===========================================

The account name is a string of max length 12 consists of 
small letters a-z, digits 1-5 and dot.

每一位有 ``26+5+1 = 32`` 种可能性，所以account name其实代表的是一个base32的字符。ads
aa ``uint64_t`` 类型的整数。

Create an Account
===========================================

Accounts are created, and it is not free.

Permissions
===========================================
