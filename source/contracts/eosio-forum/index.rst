===========================================
eosio.forum
===========================================


`eosio.forum <https://github.com/eoscanada/eosio.forum>`_
is a smart contract built by 
`EOS Canada <https://www.eoscanada.com>`_
to support the 
`EOS Referendum system <https://medium.com/@eosnationbp/the-fate-of-eos-is-in-the-hands-of-token-holders-3d345147ef6>`_
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
