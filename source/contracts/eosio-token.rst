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

.. cpp:struct:: eosio::name
  
  Mainly used to represent an eosio account name.
  Name string can only have small letters a-z, digits 1-5 or dot, and max 12 characters.
  The name is saved as a ``uint64_t``.
  More in `eosio::name source codes <https://github.com/EOSIO/eosio.cdt/blob/v1.6.2/libraries/eosiolib/core/eosio/name.hpp#L35>`_.

  .. cpp:function:: name(std::string_view str)

    Construct a new ``name`` initialising value with ``str``

  .. cpp:function:: symbol_code(uint64_t raw)

    Construct a new ``name`` initialising value with ``raw``

  .. cpp:function:: std::string to_string()

    Returns the name as a string
  
  .. cpp:function:: uint64_t raw()

    Returns the raw ``uint64_t`` value for the name

  .. cpp:function:: friend bool operator == (const name& a, const name& b)
  .. cpp:function:: friend bool operator != (const name& a, const name& b)
  .. cpp:function:: friend bool operator < (const name& a, const name& b)

  .. cpp:var:: private uint64_t value

    Stores the name as a ``uint64_t`` value

.. cpp:class:: eosio::symbol_code

  Information about a token symbol, the symbol can be 7 characters long.
  More in `eosio::symbol_code source codes <https://github.com/EOSIO/eosio.cdt/blob/v1.6.2/libraries/eosiolib/core/eosio/symbol.hpp#L29>`_.

  .. cpp:function:: symbol_code(std::string_view str)

    Construct a new ``symbol_code`` initialising value with ``str``

  .. cpp:function:: symbol_code(uint64_t raw)

    Construct a new ``symbol_code`` initialising value with ``raw``

  .. cpp:function:: std::string to_string()

    Returns the symbol name as a string
  
  .. cpp:function:: uint64_t raw()

    Returns the raw ``uint64_t`` value for the symbol

  .. cpp:var:: private uint64_t value

    Stores the symbol code as a ``uint64_t`` value

.. cpp:class:: eosio::symbol

The class ``symbol`` is used to define a token's *symbol* 
and *precision* (digits after the decimal).
For example, ``10.0000 EOS`` has *symbol* **EOS** and *precision* **4**.
It's usually written as ``{precision},{symbol}`` (in above example, ``4,EOS``).

.. cpp:class:: eosio::asset

The class ``asset`` is used to specify some amount of tokens. 
It consists of an ``amount`` property and a ``symbol`` property.
For example, ``10.0000 EOS`` is an ``asset`` with 
``amount`` equals ``10 * 10^4`` and ``symbol`` equals ``4,EOS``. 

.. cpp:class:: eosio::extended_symbol

  .. cpp:member:: int64_t amount = 0

  .. cpp:member:: eosio::symbol symbol

A type of token is created by the
:ref:`create <tutorials-token-action-create>` action. The same contract account
cannot ``create`` two types of tokens with the same ``symbol``,
but two different accounts deployed with the same ``eosio.token`` contract can
create separate tokens with identical ``symbol``.

To prevent such vulnerability,
a ``extended_symbol`` s could be equal but represent two different tokens.



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