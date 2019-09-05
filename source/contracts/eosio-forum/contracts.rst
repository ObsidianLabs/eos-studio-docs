Smart Contract
===========================================

.. cpp:namespace-push:: forum

In this section we will look at the technical details of how the 
``eosio.forum`` smart contract is implemented. This contract can be
roughly divided into four parts: *proposal*, *vote*, *status* and *post*.
All the actions and tables live in the same 
class :cpp:class:`forum`.

-------------------------------------------
Proposal
-------------------------------------------

The *proposal* part contains a table :cpp:var:`proposals` and a few
actions to operate a proposal. 

.. cpp:namespace-pop:: 

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

    Any account can execute the :cpp:func:`propose` action to create a new proposal.
    After some necessary parameter checks, a new proposal will be saved in
    table :cpp:var:`proposals` with RAM charged to the ``proposer``. 
    Each action parameter corresponds to a column in the table:

    - ``proposer`` is the account who created the proposal;
    - ``proposal_name``  is the primary key for table :cpp:var:`proposals`
      and used as the unique identifier for a proposal;
    - ``title`` is a string for the proposal title and should be less than 1024 characters;
    - ``proposal_json`` is a JSON string for the proposal description and should comply with 
      `Proposal JSON Structure Guidelines <https://github.com/eoscanada/eosio.forum#proposal-json-structure-guidelines>`_;
    - ``expires_at`` defines the deadline for the voting period, which 
      needs to be a time point between the action execution time and the next 6 months.

    Once a proposal is created, any account (including the ``proposer`` itself) 
    can vote on it via 
    the :cpp:func:`vote` action, as long as the current time is before 
    ``expires_at``.

  .. cpp:function:: ACTION expire(eosio::name proposal_name)

    This action allows the ``proposer`` to manually expire his/her
    proposal and end the voting immediately. 
    This is done by modifying the ``expires_at`` field to the current time.

    .. code-block:: cpp
      :caption: forum.cpp
      :lineno-start: 53

      proposal_table.modify(itr, proposer, [&](auto& row) {
        row.expires_at = current_time_point_sec();
      });

    The action :cpp:func:`expire` can only be called by the original ``proposer``.
    Calling it on a non-existant or already expired proposal 
    will return an error.

  .. cpp:function:: ACTION clnproposal(eosio::name proposal_name, uint64_t max_count)

    It's possible to clean a proposal if it has expired and its freeze period of 
    3 days (set by ``FREEZE_PERIOD_IN_SECONDS``) has fully elapsed.

    .. code-block:: cpp
      :caption: forum.hpp
      :lineno-start: 99

      bool can_be_cleaned_up() const { return current_time_point_sec() > (expires_at + FREEZE_PERIOD_IN_SECONDS);  }

    The action :cpp:func:`clnproposal` will clean up all votes related to a proposal.
    It works iteratively by removing as many as ``max_count`` votes,
    and can be executed multiple times until all votes are removed.

    .. code-block:: cpp
      :caption: forum.cpp
      :lineno-start: 119

      auto index = vote_table.template get_index<"byproposal"_n>();

      auto vote_key_lower_bound = compute_by_proposal_key(proposal_name, name(0x0000000000000000));
      auto vote_key_upper_bound = compute_by_proposal_key(proposal_name, name(0xFFFFFFFFFFFFFFFF));

      auto lower_itr = index.lower_bound(vote_key_lower_bound);
      auto upper_itr = index.upper_bound(vote_key_upper_bound);

      uint64_t count = 0;
      while (count < max_count && lower_itr != upper_itr) {
        lower_itr = index.erase(lower_itr);
        count++;
      }

    Notice that the secondary index ``byproposal`` is used to query and iterate
    over all votes of a given ``proposal_name`` (see table :cpp:var:`vote`).
    Once there are no more associated votes, the proposal itself will be deleted.

    .. code-block:: cpp
      :caption: forum.cpp
      :lineno-start: 134

      if (lower_itr == upper_itr && itr != proposal_table.end()) {
        proposal_table.erase(itr);
      }

    This effectively clears all the RAM consumed for a proposal 
    and all its votes.
    It's safe to allow anybody to call :cpp:func:`clnproposal` since the action will 
    only accept an expired proposal that has passed the freeze period, which means it
    has terminated its lifecycle.
    Voters, proposers, or any community member is invited to call :cpp:func:`clnproposal`
    to clean the RAM related to a proposal.


-------------------------------------------
Vote
-------------------------------------------

.. cpp:namespace-push:: forum

The *vote* part contains a table :cpp:var:`vote` as well as :cpp:func:`vote`
and :cpp:func:`unvote` actions.

.. cpp:namespace-pop::

.. cpp:class:: forum

  .. cpp:function:: ACTION vote(eosio::name voter, eosio::name proposal_name, uint8_t vote, string vote_json)

  .. cpp:function:: ACTION unvote(eosio::name voter, eosio::name proposal_name)

  .. cpp:var:: TABLE vote

    .. code-block:: cpp

      uint64_t id; // primary key
      eosio::name proposal_name; // secondary key
      eosio::name voter; // secondary key
      uint8_t vote;
      string vote_json;
      eosio::time_point_sec updated_at;

    For a non-expired proposal, any accounts can use the :cpp:func:`vote`
    action to publish a vote. It will consume a little bit of RAM from the voter (430 bytes)
    to save the vote info in table :cpp:var:`vote`.
    
    The meaning of the vote is represented by 
    the ``vote`` field.

    - ``0`` means *no*
    - ``1`` means *yes*
    - ``255`` means *abstain*
    - Other values can be used to represent other meanings

    In table :cpp:var:`vote`,
    the primary key ``id`` is generated automatically. Secondary keys are
    created for fields ``proposal_name`` and ``voter`` to support searching
    by proposal or voter.
    The field ``vote_json`` is designed to provide extra information for a vote,
    such as a comment explaining the thought behind the vote.

    The voter can execute the :cpp:func:`vote` action again to change his/her vote,
    or call the :cpp:func:`unvote` action to delete his/her vote in table :cpp:var:`vote`.
    Removing the current active vote reclaims the stored RAM of the vote. 
    Of course, the vote will not count anymore.

    The :cpp:func:`vote` and :cpp:func:`unvote` actions will first check
    whether the proposal is still active, and refuse the execution if the proposal is 
    expired.

    .. code-block:: cpp
      :caption: forum.hpp
      :lineno-start: 98

      bool is_expired() const { return current_time_point_sec() >= expires_at; }

    Therefore, it is guaranteed that the vote statistics for a proposal will be fixed
    once the proposal is expired, so that people will be able to count the votes
    and compute the voting result.


-------------------------------------------
Status
-------------------------------------------

.. cpp:class:: forum

  .. cpp:function:: ACTION status(eosio::name account, string content)

  .. cpp:var:: TABLE status

    .. code-block:: cpp

      // scope is self
      eosio::name account; // primary key
      string content;
      eosio::time_point_sec updated_at;


    The action :cpp:func:`status` will record a status for the associated ``account``. 
    If the ``content`` is empty, the action will remove a previous status.
    Otherwise, it will add a status entry or modify the existing entry for 
    the ``account`` using the ``content`` received.

.. todo::

  How this is used?

-------------------------------------------
Post
-------------------------------------------

.. cpp:class:: forum

  .. cpp:function:: ACTION post(eosio::name poster, string post_uuid, string content, eosio::name reply_to_poster, string reply_to_post_uuid, bool certify, string json_metadata)

  .. cpp:function:: ACTION unpost(eosio::name poster, string post_uuid)

.. todo::

  How this is used?
