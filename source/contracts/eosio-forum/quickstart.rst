===========================================
Quickstart
===========================================

In the first section we will
demonstrate the basic workflow of create a proposal
and how to cast a vote on it. This workflow will be divided into 4 stages: 
`proposal`, `voting`, `freeze`, and `clearing the RAM`.

We will use 
:doc:`EOS Studio Web </getting-started/web>` and dfuse's On Demand Network for
the demonstration so that you will not need to set up a development
environment.

.. cpp:namespace:: forum

Setups
===========================================

- Open ``eosio.forum`` at 
  https://app.eosstudio.io/dfuseio/eosio.forum and click the `fork` button
  to make your own copy of the project.

- Edit a waiting time from 3 days to 5 minutes so that we won't 
  need to wait that long in this demo. 
  Open the file ``include/forum.hpp``, go to line 70 and change the value of
  ``FREEZE_PERIOD_IN_SECONDS`` to ``300``. The use of this value will be
  explained later.

- Click the `build` button to rebuild the smart contract and 
  regenerate the ``wasm`` and ``abi`` files.

- Create a new account and deploy the contract you just built. For easy reference
  we will assume the account name to be ``eosio.forum``.

- Create some other accounts, one for creating a proposal (``proposer``) 
  and as many others for casting votes as you'd like (``voter1``, ``voter2``, etc).

Stage 1: Proposal
===========================================

Anybody with a valid EOS account 
can create a proposal to the community. Such operation will consume some RAM
to save the content of the proposal, so before we create a new one, let's
open the account ``proposer`` and take a note of its current RAM usage.
Then, go to EOS Studio's :doc:`Contract Inspector </eos-studio/contract>` 
and execute the :cpp:func:`propose` action

.. code-block:: js

  // Execute action *propose* with parameters
  // Use proposer@active to sign
  proposer: "proposer"
  proposal_name: "usesys"
  title: "Use SYS as the core symbol"
  proposal_json: { "type": "referendum-v1", "content": "Should EOS change its symbol to SYS?" }
  expires_at: "2020-01-01T00:00:00"

The transaction should be signed by ``proposer``. All ongoing proposals are
indexed by ``proposal_name``, which is of type :cpp:class:`eosio::name` and 
therefore can have a human readable name. The content of the proposal
contains a ``title`` (up to 1024 chars) and a ``proposal_json`` in the
format of
`Proposal JSON Structure Guidelines <https://github.com/eoscanada/eosio.forum#proposal-json-structure-guidelines>`_.
Every proposal also needs to have an expiration time ``expires_at``,
which should be a point between now and 6 months later.
We will explain how it is used in :ref:`Stage 3 <Stage 3: Freeze>`.

If you check ``proposer``'s RAM usage now, you should see it has increased
by about 1 KB if you have the same parameters as provided above. Requirement 
of some RAM can prevent spammings  
so important issues can be discussed and voted on. Once a proposal has
finished its entire lifecycle, ``eosio.forum`` allow 
you to safely remove the proposal and free up used RAM (:ref:`Stage 4 <Stage 4: Clean up>`).

Published proposals are recorded in the :cpp:var:`proposals` table.
We can check it to see the proposal we just published.

=============  =============  =============  =============  =============  =============
proposal_name  proposer       title          proposal_json  created_at     expires_at
=============  =============  =============  =============  =============  =============
usesys         proposer       Use SYS ...    {"type": ...}  2019-09...     2020-01...
=============  =============  =============  =============  =============  =============


Stage 2: Voting
===========================================

Once the proposal has been created, people can start to vote on it 
via the :cpp:func:`vote` action. Let's make some votes using the
previously created accounts.

.. code-block:: js

  // Execute action *vote* with parameters
  // Use voter1@active to sign
  voter: "voter1"
  proposal_name: "usesys"
  vote: 1 // positively vote
  vote_json: ""

.. code-block:: js

  // Execute action *vote* with parameters
  // Use voter2@active to sign
  voter: "voter2"
  proposal_name: "usesys"
  vote: 0 // negative vote
  vote_json: ""

.. code-block:: js

  // Execute action *vote* with parameters
  // Use voter3@active to sign
  voter: "voter3"
  proposal_name: "usesys"
  vote: 255 // abstain
  vote_json: ""

Notice that the ``vote`` value is used to represent `yes` (``1``) or `no` (``0``) to 
the proposal. It ranges from ``0`` to ``255`` so other values can be used to represent
special meanings.

Votes are saved in table :cpp:var:`vote`

=============  =============  =============  =============  =============  =============
id             proposal_name  voter          vote           vote_json      updated_at
=============  =============  =============  =============  =============  =============
0              usesys         voter1         1                             2019-09...
1              usesys         voter2         0                             2019-09...
2              usesys         voter3         255                           2019-09...
=============  =============  =============  =============  =============  =============

Similar as above, voters need to pay RAM to save their own votes. If you compare
voters' RAM usage you can see it has increased a little bit
after executing the :cpp:func:`vote` action. These RAM will also be refunded to each 
voter when the proposal is removed in :ref:`Stage 4 <Stage 4: Clean up>`.

A voter can change vote anytime by calling :cpp:func:`vote` again 
with a different ``vote`` value to override the old one. 
S(he) can also `remove` the vote via the :cpp:func:`unvote` action, which would
completely remove his/her vote data in the :cpp:var:`vote` table and refund RAM immediately.


Stage 3: Freeze
===========================================

A proposal cannot be indefinitely continued, so an expiration time is needed to
set a deadline for the voting time. That's why the ``expires_at`` was predefined
when the proposal was created. The proposer can also decide to end 
the proposal ahead of time by manually calling the :cpp:func:`expire` action.
This amends the proposal's ``expires_at`` field to the current time instead of 
waiting for its original expiration date to be reached.

Let's expire our proposal now

.. code-block:: js

  // Execute action *expire* with parameters
  // Use proposer@active to sign
  proposal_name: "usesys"

Once a proposal is expired (be it manually or automatically if it passed its
expiration date), the proposal will enter a 3 day *freeze* period.
Within this freeze period, the proposal is locked and no actions can be called 
on it (no vote changes, no vote removal and no clean up).
It is to allow a period that anyone can query the blockchain data, count votes
and generate the voting result independently. The implementation of the proposal 
will be determined by the result.

Since we modified the freeze time in the sourced code, we just need to wait
5 minutes instead of 3 days. Within those 5 minutes, you will find that all attempts
to execute the :cpp:func:`vote` or :cpp:func:`unvote` actions related to the freezing proposal
will fail.

After the freeze period has passed, the process of handling 
a proposal through democratic voting has completed. Now, we can safely
reclaim the RAM used in creating the proposal and generating votes.


Stage 4: Clean up
===========================================

Once the freeze period has passed after a proposal's expiry, 
any account can use the :cpp:func:`clnproposal` action to free all of the 
associated used RAM.
This action effectively reclaims all RAM consumed for votes and for the 
proposal itself. The RAM is thus given back to voters (for their votes) and to 
the proposer (for the proposal).

The :cpp:func:`clnproposal` action can be called by anybody. 
There is no risk since only expired proposals that have passed their freeze 
period can be cleaned.

.. code-block:: js

  // Execute action *clnproposal* with parameters
  // Use any account to sign
  proposal_name: "usesys"
  max_count: 100

If there are many voters, the action :cpp:func:`clnproposal` will remove
up to ``max_count`` votes in one execution to prevent transaction timeout. 
Once all votes are removed on a proposal, the proposal itself will then be removed.

Now, if you look at accounts ``proposer`` and ``voter1`` etc, their RAM usage should 
go back to the value before participating in the proposal and voting. 
Therfore, on completion of a proposal all used memory will be returned 
without a memory leak.