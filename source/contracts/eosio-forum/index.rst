===========================================
eosio.forum
===========================================

.. note::

  A brief introduction of eosio.forum

The purpose of ``eosio.forum`` is to support the EOS Referendum system 
by storing proposals and their related votes in-RAM in the blockchain's 
state.

It's also possible to create posts, responses related to those posts, and statuses, 
but they are not stored in-RAM in the blockchain's state. 
It allows authenticated 
messages to go through, where they are visible in the transaction history 
of the chain. Off-chain tools are needed to sort, display, aggregate, 
and report on the outputs of the post and status actions.


.. toctree::
  :hidden:

  quickstart
  referendum
  contracts
