===========================================
Accounts
===========================================

Blockchains like Bitcoin or Ethereum use `addresses` to represent 
individuals in transactions. Tokens are transfered from one address 
to another, and each address has its own token balance. 
However, EOSIO-based blockchain use `accounts` as the basic unit 
to store tokens and act as individuals in blockchain transactions.

Account Name
===========================================

An EOSIO account can have a human readable name so it will be easy to remember.
The account name is a string of max length 12 consists of 
small letters a-z, digits 1-5 and dot.

..
  Each char has  ``26+5+1 = 32`` possibilities, 
  so an account name is actually a ``base32`` string. 

  Its value is a ``uint64_t`` interger


Create an Account
===========================================

A new EOSIO account needs to be created by another existing account. 
Creating an account will require some EOS tokens to purchase the 
:doc:`resources <resource>`
needed to store the account's basic information, such as its own name and 
token balance.


Permissions
===========================================

A special feature EOSIO offers is that an account can process 
multiple levels of permissions, each of which has unequal authorities 
to approve different sets of transactions.  
Such design can provide greater security for EOSIO blockchains
because users can set up and use a low-privilege permission in daily transactions.
Losing this permission wouldn't cause too much damage because it can only
perform limited transactions, and users can use a high-privilege permission 
to recover the lost one.

.. Every EOS account has two native named permissions (owner and active).
