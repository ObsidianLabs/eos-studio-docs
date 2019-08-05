Smart Contract
===========================================

.. note::

  This section will go through the ``eosio.forum`` smart contract,
  including actions and table structures. Because this section will mainly 
  target developers, it might be helpful to be very specific and
  cite source codes at some places.

-------------------------------------------
Actions
-------------------------------------------

propose
-------------------------------------------

.. cpp:function:: ACTION forum::propose(eosio::name proposer, eosio::name proposal_name, string title, string proposal_json, eosio::time_point_sec expires_at)

Propose a new proposal to the community.

expire
-------------------------------------------

.. cpp:function:: ACTION forum::expire(eosio::name proposal_name)

Immediately expires a currently active proposal. The proposal can only be expired 
by the original proposer that created it. It's not valid to expire an already expired 
proposal.


vote
-------------------------------------------

.. cpp:function:: ACTION forum::vote(eosio::name voter, eosio::name proposal_name, uint8_t vote, string vote_json)

Vote for a given proposal using your account.

unvote
-------------------------------------------

.. cpp:function:: ACTION forum::unvote(eosio::name voter, eosio::name proposal_name)

Remove your current active vote, effectively reclaiming the stored RAM of the vote. 
Of course, your vote will not count anymore (neither positively or negatively) on 
the current proposal's voting statistics.

It's **not** possible to ``unvote`` on a proposal that is expired but within its freeze 
period of 3 days. If the proposal is expired and the freeze period has elapsed, 
it's possible to ``unvote`` on the proposal. To be nice to the community however, 
you should call ``clnproposal`` until the proposal is fully cleaned up so that every 
vote will be removed and RAM will be freed for all voters.

clnproposal
-------------------------------------------

.. cpp:function:: ACTION forum::clnproposal(eosio::name proposal_name, uint64_t max_count)

Clean a proposal from all its votes and the proposal itself once there are no more 
associated votes. The action works iteratively, receiving a ``max_count`` value. It removes 
as many as ``max_count`` votes. When there are no more votes, the proposal itself is deleted.

This effectively clears all the RAM consumed for a proposal and all its votes. Call the 
action multiple times until all votes are removed.

It's possible to clean a proposal only if it has expired and if its freeze period of 
3 days has fully elapsed. Within the freeze period, the proposal is locked and no actions 
can be performed on it. Since only expired proposals can be cleaned, anybody can invoke 
this action, no authorization is required. Voters, proposers, or any community member 
is invited to call ``clnproposal`` to clean the RAM related to a proposal.

.. note::
  
  Since a proposal can expire only by a manual action issued by the proposal's author or 
  if it has passed its ``expires_at`` value, it's safe to be called by anybody since the proposal
  has effectively terminated its lifecycle.



post
-------------------------------------------

.. cpp:function:: ACTION forum::post(eosio::name poster, string post_uuid, string content, eosio::name reply_to_poster, string reply_to_post_uuid, bool certify, string json_metadata)

unpost
-------------------------------------------

.. cpp:function:: ACTION forum::unpost(eosio::name poster, string post_uuid)

status
-------------------------------------------

.. cpp:function:: ACTION forum::status(eosio::name account, string content)

Record a status for the associated ``account``. If the content is empty, the action
will remove a previous status. Otherwise, it will add a status entry for the ``account`` 
using the ``content`` received.


-------------------------------------------
Tables
-------------------------------------------


proposals
-------------------------------------------

Table structure of ``proposals``


status
-------------------------------------------

Table structure of ``status``


vote
-------------------------------------------

Table structure of ``vote``