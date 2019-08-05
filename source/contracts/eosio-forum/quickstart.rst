===========================================
Quickstart
===========================================

.. note::

  Before dive into the details of ``eosio.forum``, I think it's
  helpful to briefly demonstrate the basic workflow of create a proposal
  and how to vote for it. The workflow will be divided into 4 stages -- 
  proposal, voting, freeze, and clean up -- as described below.
  
  We will use EOS Studio Web and dfuse's on-demand network for
  the demonstration so users don't need to set up a development
  environment.


Setups
===========================================

Possible steps:

- Open dGoods smart contract in EOS Studio Web (https://app.eosstudio.io/eoscanada/eosio.forum)

- Create an account to deploy the ``eosio.forum`` smart contract. 
  Before deployment, we need to edit the freeze waiting time in source code to 5 minutes,
  so when we go to the freeze period in this demo we don't need to wait 3 days.

- Create some other accounts, one for creating a proposal and others for votings.

Stage 1: Proposal
===========================================

This is the stage to create a new proposal.

Need to use an account to create the proposal by executing ``propose``action. Need to provide:

- Proposal name (as its id)
- Title
- Proposal (in json format)
- Expiration time

Besides, we will also show

- How to look at published proposal in the ``proposals`` table;
- The proposer's RAM usage will increase as a result of creating proposal.

Stage 2: Voting
===========================================

This is the stage for people to vote.

- Use other accounts to vote for yes/no (``vote`` action)
- Change vote (call ``vote`` action again)
- Withdraw the vote (``unvote`` action)

Besides, we will also show

- How to look at votes in the ``vote`` table;
- The voter's RAM usage will increase as a result of voting.

Stage 3: Freeze
===========================================

Once the proposal's expiration time is reached, or the ``expire`` action is 
executed by the proposer, the proposal will enter a 3 day freeze period.
Within this freeze period, the proposal is locked and no actions can be called 
on it (no vote changes, no vote removal and no clean up). This is to 
allow a period where multiple tools can query the results for cross-verification.

Since we modified the freeze time before deployment, we just need to wait for 5 minutes
instead of 3 days. Within the 5 minutes, we can see execution of ``vote`` or ``unvote``
actions will fail.

Stage 4: Clean up
===========================================

Once a proposal has ended its freeze period, any account can use the ``clnproposal`` action
to free used RAM.

- Use a different account to execute ``clnproposal``
- Look at the proposer and voters' account and see their used RAMs are freed

.. note::

  The goal of this section is to give readers a perliminary understanding of ``eosio.forum``.
  It can also serve as a good instruction to demonstrate how EOSIO Referendum works.