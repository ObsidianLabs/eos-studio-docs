Smart Contract
===========================================

.. note::

  This section will explain the ``eosio.forum`` smart contract, including
  actions and tables.

In the following sections we describe the minimum set of required 
methods and functionality for implementing this specification, along with 
the table structure.

-------------------------------------------
Actions
-------------------------------------------

setconfig
-------------------------------------------

.. cpp:function:: ACTION forum::propose(eosio::name proposer, eosio::name proposal_name, string title, string proposal_json, time_point_sec expires_at)



