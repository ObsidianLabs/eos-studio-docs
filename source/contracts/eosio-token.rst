.. _contract-token:

===========================================
eosio.token
===========================================

Introduction
===========================================

The ``eosio.token`` contract defines the structures and actions that allow users 
to create, issue, and manage tokens on eosio based blockchains.

See here https://app.eosstudio.io/eosio/eosio.token

Types
===========================================

There are a few types used in ``eosio.token`` as basic data structure.
They are also used extensively in other EOSIO contracts.

Smart Contract
===========================================

--------------------
Actions
--------------------

.. _tutorials-token-action-create:

create
--------------------

.. cpp:function:: ACTION token::create(const eosio::name &issuer, const eosio::asset &maximum_supply)

Create a token in supply of ``maximum_supply`` with an ``issuer`` account.
If successful, a new entry in :ref:`stats <tutorials-token-table-stats>`
table for token symbol scope will be created. 
Transaction must be signed by the contract account itself.


issue
-------------------------------------------

.. cpp:function:: ACTION token::issue(const eosio::name &to, const eosio::asset &quantity, const string &memo)

Issue ``quantity`` of tokens to account ``to``
with an optional ``memo`` that accompanies the token issue transaction. 
The token needs to be created in advance.
Transaction must be signed by the ``issuer``.


transfer
-------------------------------------------

.. cpp:function:: ACTION token::transfer(const eosio::name &from, const eosio::name &to, const eosio::asset &quantity, const string &memo)

Transfer ``quantity`` of tokens from account ``from`` to account ``to``,
with an optional ``memo`` that accompanies the transfer transaction. 
The token needs to be created in advance.
Transaction must be signed by account ``from``.

.. _tutorials-token-action-open:

open
-------------------------------------------

.. cpp:function:: ACTION token::open(const eosio::name &owner, const eosio::symbol &symbol, const eosio::name &ram_payer)

Allows ``ram_payer`` to create an account ``owner`` with zero balance for
token ``symbol`` at the expense of ``ram_payer``.
Transaction must be signed by account ``ram_payer``.


close
-------------------------------------------

.. cpp:function:: ACTION token::close(const eosio::name &owner, const eosio::symbol &symbol)

This action is the opposite for :ref:`open <tutorials-token-action-open>`, 
it closes the account ``owner`` for token ``symbol``.


retire
-------------------------------------------

.. cpp:function:: ACTION token::retire(const eosio::asset &quantity, const string &memo)

The opposite for :ref:`create <tutorials-token-action-create>` action.
If all validations succeed, it debits the statstable.supply amount.


-------------------------------------------
Tables
-------------------------------------------

.. _tutorials-token-table-stats:

stats
-------------------------------------------

accounts
-------------------------------------------