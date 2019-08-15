Smart Contract
===========================================

In this section we will go to more technical details of how the 
``eosio.forum`` smart contract is implemented. 
including actions and table structures.

-------------------------------------------
Proposal
-------------------------------------------


.. cpp:class:: forum

  .. cpp:function:: ACTION propose(eosio::name proposer, eosio::name proposal_name, string title, string proposal_json, eosio::time_point_sec expires_at)

  .. cpp:var:: TABLE proposals

    .. code-block:: cpp

      eosio::name proposal_name; // primary key
      eosio::name proposer; // secondary key
      string title;
      string proposal_json;
      eosio::time_point_sec created_at;
      eosio::time_point_sec expires_at;

    The :cpp:func:`propose` action will create a new proposal
    and save it in table :cpp:var:`proposals`.
    Most of their parameters are identical.

    - ``proposal_name``  is the unique id of a proposal
    - ``proposer`` is the account who created the proposal
    - ``title`` is a brief description of the proposal and should be less than 1024 characters
    - ``proposal_json``: is a JSON string for proposal descriptions and should in format of 
      `Proposal JSON Structure Guidelines <https://github.com/eoscanada/eosio.forum#proposal-json-structure-guidelines>`_
    - ``expires_at``: must be a future time but not over 6 months

    A proposal can be removed by :cpp:func:`clnproposal` 
    only after it has expired and passes the 3-day freeze period.

  .. cpp:function:: ACTION expire(eosio::name proposal_name)

    Immediately expires a currently active proposal. This action can
    only be called by the original proposer. Calling :cpp:func:`expire` 
    on an already expired proposal will return an error.


  .. cpp:function:: ACTION vote(eosio::name voter, eosio::name proposal_name, uint8_t vote, string vote_json)

    Vote for a given proposal using your account.

    Can be executed again to change your vote.


  .. cpp:function:: ACTION unvote(eosio::name voter, eosio::name proposal_name)

    Remove your current active vote, effectively reclaiming the stored RAM of the vote. 
    Of course, your vote will not count anymore (neither positively or negatively) on 
    the current proposal's voting statistics.

    It's **not** possible to ``unvote`` on a proposal that is expired but within its freeze 
    period of 3 days. If the proposal is expired and the freeze period has elapsed, 
    it's possible to ``unvote`` on the proposal. To be nice to the community however, 
    you should call ``clnproposal`` until the proposal is fully cleaned up so that every 
    vote will be removed and RAM will be freed for all voters.


  .. cpp:function:: ACTION clnproposal(eosio::name proposal_name, uint64_t max_count)

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

    Since a proposal can expire only by a manual action issued by the proposal's author or 
    if it has passed its ``expires_at`` value, it's safe to be called by anybody since the proposal
    has effectively terminated its lifecycle.


  .. cpp:function:: ACTION post(eosio::name poster, string post_uuid, string content, eosio::name reply_to_poster, string reply_to_post_uuid, bool certify, string json_metadata)


  .. cpp:function:: ACTION unpost(eosio::name poster, string post_uuid)


  .. cpp:function:: ACTION status(eosio::name account, string content)

    Record a status for the associated ``account``. If the content is empty, the action
    will remove a previous status. Otherwise, it will add a status entry for the ``account`` 
    using the ``content`` received.


-------------------------------------------
Tables
-------------------------------------------

.. cpp:class:: forum


    Saves all ongoing proposals created by the :cpp:func:`propose` action.


  .. cpp:var:: TABLE vote

    .. code-block:: cpp

      uint64_t id; // primary key
      eosio::name proposal_name; // secondary key
      eosio::name voter; // secondary key
      uint8_t vote;
      string vote_json;
      eosio::time_point_sec updated_at;

    vote table

  .. cpp:var:: TABLE status

    .. code-block:: cpp

      // scope is self
      eosio::name account; // primary key
      string content;
      eosio::time_point_sec updated_at;

    status table

