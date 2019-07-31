===========================================
Account System
===========================================

For Bitcoin or Ethereum, `addresses` are the individuals 
in blockchain transactions. For example, tokens are
transfered from one address to another, and each address has
its own token balance. 
Different from them, EOSIO uses `accounts` as the basic unit 
to store tokens and act as an individual on the blockchain.

Account Name
===========================================

An account can have a human readable name so it will be easy to remember.
The account name is a string of max length 12 consists of 
small letters a-z, digits 1-5 and dot.

..
  Each char has  ``26+5+1 = 32`` possibilities, 
  so an account name is actually a ``base32`` string. 

  Its value is a ``uint64_t`` interger

Create an Account
===========================================

Accounts need to be created by another account.

Accounts are created, and it is not free.

Permissions
===========================================

An account has a few permission keys (a private key and a public key)
Using permission key to sign a transaction issued by an account.
