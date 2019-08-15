===========================================
eosio.token
===========================================

Introduction
===========================================

The ``eosio.token`` contract defines the structures and actions that allow users 
to create, issue, and manage tokens on eosio based blockchains.
The core token ``EOS`` of the EOSIO mainnet are issued under 
the account ``eosio.token`` using this smart contract.

- GitHub repo: https://github.com/EOSIO/eosio.contracts/tree/master/contracts/eosio.token
- EOS Studio: https://app.eosstudio.io/eosio/eosio.token

Types
===========================================

There are a few types used in ``eosio.token`` as basic data structures.
You can click the link in the action definitions to see how the types are defined.

Smart Contract
===========================================

--------------------
Actions
--------------------

.. cpp:class:: token

  .. cpp:function:: ACTION create(eosio::name issuer, eosio::asset maximum_supply)

    Create a token in supply of ``maximum_supply`` with an ``issuer`` account.
    If successful, a new entry in :cpp:var:`stat`
    table for token symbol scope will be created. 
    Transaction must be signed by the contract account itself.


  .. cpp:function:: ACTION issue(eosio::name to, eosio::asset quantity, string memo)

    Issue ``quantity`` of tokens to account ``to``
    with an optional ``memo`` that accompanies the token issue transaction. 
    The token needs to be created in advance.
    Transaction must be signed by the ``issuer``.


  .. cpp:function:: ACTION transfer(eosio::name from, eosio::name to, eosio::asset quantity, string memo)

    Transfer ``quantity`` of tokens from account ``from`` to account ``to``,
    with an optional ``memo`` that accompanies the transfer transaction. 
    The token needs to be created in advance.
    Transaction must be signed by account ``from``.


  .. cpp:function:: ACTION open(eosio::name owner, eosio::symbol symbol, eosio::name ram_payer)

    Allows ``ram_payer`` to create an account ``owner`` with zero balance for
    token ``symbol`` at the expense of ``ram_payer``.
    Transaction must be signed by account ``ram_payer``.


  .. cpp:function:: ACTION close(eosio::name owner, eosio::symbol symbol)

    This action is the opposite for :cpp:func:`open`, 
    it closes the account ``owner`` for token ``symbol``.


  .. cpp:function:: ACTION retire(eosio::asset quantity, string memo)

    The opposite of :cpp:func:`create`.
    If all validations succeed, it debits the statstable.supply amount.


-------------------------------------------
Tables
-------------------------------------------

.. cpp:class:: token

  .. cpp:var:: TABLE stat

    .. code-block:: cpp

      // scope is token symbol
      eosio::asset supply; // supply.symbol is the primary key
      eosio::asset max_supply;
      eosio::name issuer;


  .. cpp:var:: TABLE accounts

    .. code-block:: cpp

      // scope is owner
      eosio::asset balance; // balance.symbol is the primary key

