===========================================
EOSIO Referendum
===========================================

  *"The price good men pay for indifference to public affairs is to be ruled by evil men."*
  
    -- Plato

Motivation
===========================================

EOSIO is a self-governed blockchain which encourages its token holders
to share their voice through proposals and voting as a means for consensus.

`Block.one <https://block.one/>`_ had proposed to create an 
`on-chain forum <https://github.com/EOSIO/eosio.forum>`_
so that messages could be posted to the blockchain, 
allowing for verification of a user’s opinion on a matter.
`EOS Canada <https://www.eoscanada.com>`_
recognized that if we used this messaging system to post 
approval or disapproval of a question, we would have the 
underlying structure of a *referendum system*. It can be used to poll 
a community to discover a communal opinion on a topic or question. 

As a self-governed blockchain, token holders
will use their stake to vote on proposals and decide on questions that 
impact changes to the governing documents,
system-level Ricardian Contracts, 
or the `EOSIO codebase <https://github.com/EOSIO/eos>`_. 
A simple proposal or poll allows the EOS community’s voice to be heard 
on specific matters without changing any contracts or system code. 
A referendum, if passed, should produce an actionable result to enact 
a change on the EOS mainnet, as decided by the EOS community.

The referendum is crucial to ensure EOSIO blockchain to be decentralized 
yet self-evolving. EOSIO codebase, governing documents and usage of network fund
can be modified through token holder consensus by way of referendum 
through proposing, voting, and ratification. This principle is written in the
`EOS User Agreement (EUA) <https://github.com/eosnewyork/eosuseragreement>`_:

  **Article IV   Governing Documents** - 
  Any modifications to the `EUA` and `governing documents` may be made by ``eosio.prods``. 
  It is admonished that a statement be crafted and issued through ``eosio.prods``
  via ``eosio.forum`` referendum contract describing such a modification in advance.

  **Article VII   Network Funds** -
  It is admonished that any altering of the state of any tokens contained within 
  network fund accounts, or altering any pre-existing code that directly or indirectly 
  governs the allocation, fulfillment, or distribution of any `network funds` be preceded 
  by a statement crafted and issued by eosio.prods to the ``eosio.forum`` referendum 
  system contract describing the effect in advance.


Implementation
===========================================

EOS Canada had taken the lead in writing the code that drives 
the referendum system that is being proposed for the EOS mainnet.
They took Block.one’s 
`sample contract <https://github.com/EOSIO/eosio.forum>`_
and started refactoring it to meet
the community’s needs as discovered through long back-and-forth discussions 
with other teams who were involved.

In July 2018, EOS Canada first deployed the beta version to the account ``eosforumdapp``, 
to run it through some further testing, and to gather some community feedback.
Later, the smart contract was deployed to another account ``eosforumrcpp``, 
which stands for EOS Forum Release Candidate Pre-Production.
EOS Canada also modified the permission structure for this account to be a shared multisig, 
set up amongst the other block producers who have been working alongside them.


On EOS Mainnet
===========================================

After many weeks of testing, the ``eosio.forum`` contract was ready for the release.
EOS Canada proposed that this contract 
`deployed to a system contract account <https://github.com/eoscanada/proposals/blob/master/deployforum.md>`_ 
``eosio.forum`` so that all users know that this is the official referendum contract 
that they can have faith in. The smart contract would also be fully under the control of the 
Block Producers collectively, and thus of the EOS mainnet itself.

The EOS community has approved this proposal in January 2019, so the ``eosio.forum``
has formally become the official referendum system on the EOS mainnet.
Since then, EOS token holders have submitted numerous proposals on this contract 
to improve the mainnet governance and promote the development of the EOS mainnet.
For example, the
`EOS User Agreement <https://github.com/eosnewyork/eosuseragreement>`_
itself was adopted through
`a referendum <https://eosauthority.com/polls_details?proposal=eosuseragree_20190207>`_
on ``eosio.forum`` in May 2019.

To encourage EOS holders to actively participate in voting and community governance,
many teams have created user interfaces that make it easy to view proposals and 
cast a vote. This is a list of them:

- EOSX (by EOS Asia):
  https://www.eosx.io/tools/referendums/proposals

- Bloks.io (by EOS Cafe & HKEOS):
  https://bloks.io/vote/referendums

- EOS Authority:
  https://eosauthority.com/polls

- EOS Forum (by novusphere.io):
  https://eos-forum.org/#/e/referendum

- EOS Toolkit (by GenerEOS):
  https://eostoolkit.io/community/forum/vote

- EOSC (by EOS Canada):
  https://github.com/eoscanada/eosc

- EOSVotes (by BP Community):
  https://eosvotes.io

- EOS Voter (by Greymass):
  https://github.com/greymass/eos-voter/releases


On Sidechains
===========================================

The same smart contract has also been adopted in other EOSIO sidechains.
For example, `BOSCore <https://boscore.io/en/index.html>`_ deployed
its own version of 
`forum contract <https://github.com/boscore/referendum/tree/master/contracts/eosio.forum>`_
to support its referendum and Worker Proposal System (WPS).
They also use the referendum system to launch the
`BOS Development Incentive Program <https://github.com/boscore/Documentation/blob/master/Referendum_WPS/BOS%20Ecosystem%20Program(Referendum%20%26%20WPS).md>`_
to incentive technical contributions to its ecosystem.

.. todo::

  Is ``eosio.forum`` being used by other sidechains?

Other Applications
===========================================

The ``eosio.forum`` can be used in many other scenarios.
An interesting example is that the EOS community was able to use ``eosio.forum`` 
to create an on-chain Reddit substitute called `Novusphere <https://novusphere.io/>`_.
The forum contract is like the backend server for Novusphere, and is has 
another web user interface for users to easily create or read posts.
You can go to their website at https://discussions.app, and 
compare the posts with ``eosio.forum``'s 
`transaction history <https://eosq.app/search?q=receiver%253Aeosio.forum%2520action%253Apost>`_.

.. todo::

  Some other examples