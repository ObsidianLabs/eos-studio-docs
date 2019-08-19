===========================================
Quickstart
===========================================

.. cpp:namespace:: dgoods

Before dive into details, we will first go through the key steps of dGoods standard 
and learn how to issue some digital assets. For those who only need to use dGoods in 
some basic scenarios, this section should be enough for you to understand some common practices of 
dGoods.

We will use :doc:`EOS Studio Web </getting-started/web>`
and the :ref:`Cloud-hosted Network <Cloud-hosted Network>` in this tutorial
so you don't need to set up an EOSIO development environment.
The source code for dGoods smart contract is already on EOS Studio Web. 
Open the project at https://app.eosstudio.io/mythicalgames/dgoods,
which also includes pre-built ``wasm`` and ``abi``.

Preparations
===========================================

Assume we want to build a game and use blockchain technology to grant players true
ownership of in-game currencies and items. There are many different kinds of items
in a game, and one benefit of dGoods is you can use a single contract to manage all
of them.

Before started, you need to

- Create a main account for the game and deploy the dGoods smart contract. 
  For easy reference, we will call it ``diablo.dapp``

- Add ``eosio.code`` permission to ``diablo.dapp@active``

- Create two accounts ``demonhunter`` and ``necromancer`` as players in the game

Initialization
===========================================

Each dGoods contract has a universal ``symbol`` to govern all digital assets created under it.
The ``symbol`` can be any strings of capital letters up to 7 characters.
To initialize a new dGoods contract, it's required to first execute the :cpp:func:`setconfig` action 
to set the ``symbol`` along with some other configs.
Open ``diablo.dapp`` contract in EOS Studio's :doc:`Contract Inspector </eos-studio/contract>` and 
execute the :cpp:func:`setconfig` action with

.. code-block:: js

  // Execute action *setconfig* with parameters
  symbol: "DIABLO"
  version: "1.0"

Lifecycle of digital assets
===========================================

Digital assets usually go through the following process to be created and 
then circulated among different owners.

- When a type of asset is *created*, the basic properties are locked-in on the blockchain
- Created assets can be *issued* to some accounts as initial owners
- The owner of an asset can *transfer* or *sell* it to another account
- *Burnable* assets can be permanently destroyed (usually) by the owner

-------------------------------------------
Create
-------------------------------------------

The dGoods contract use a two level structure ``category:token_name`` to classify its assets.
For example, if we want to create *sword* items for our game, we can use ``category=weapons`` 
and ``token_name=sword``. Each sword is unique because they have different attributes, 
and this type of :cpp:class:`eosio::asset` is called *non-fungible token (NFT)*.

To create the sword asset type, execute the :cpp:func:`create` action

.. code-block:: js

  // Execute action *create* with parameters
  issuer: "diablo.dapp"
  rev_partner: "diablo.dapp"
  category: "weapons"
  token_name: "sword"
  fungible: false
  burnable: true // can be burned (destroyed)
  sellable: true // can be sold in the built-in DEX
  transferable: true // can be transfered
  rev_split: 0.05
  base_uri: "http://dgoods.eosstudio.io/quickstart/weapons/sword/"
  max_supply: "10000 SWORD"

Here we created ``weapons:sword`` with max supply of 10000, which means 
there will be at maximum 10000 swords for all players in the game.
The ``base_uri`` will be used later to provide metadata for each sword.
Properties ``rev_partner`` and ``rev_split`` are used when the asset is sold in the 
built-in :ref:`decentralized exchange <Decentralized Exchange>`.

We will use the :cpp:func:`create` action again to define an in-game currency.

.. code-block:: js

  // Execute action *create* with parameters
  issuer: "diablo.dapp"
  rev_partner: "diablo.dapp"
  category: "currencies"
  token_name: "gold"
  fungible: true
  burnable: true
  sellable: false
  transferable: true
  rev_split: 0
  base_uri: "http://dgoods.eosstudio.io/quickstart/currencies/gold/"
  max_supply: "1000000000.0000 GOLD"

Each unit of ``GOLD`` will be identical and this type of asset is called *fungible tokens*.
Note that for both fungible and non-fungible tokens, ``max_supply`` is always of type ``asset`` 
which carries a symbol and a precision. For NFTs the precision must be an integer.

-------------------------------------------
Issue
-------------------------------------------

Now we have created two types of assets, and we are free to issue them now
using the :cpp:func:`issue` action.

.. code-block:: js

  // Execute action *issue* with parameters
  to: "demonhunter"
  category: "weapons"
  token_name: "sword"
  quantity: "1 SWORD"
  relative_uri: "master_sword"
  memo: "You justed picked the Master Sword!"

dGoods standard allows you to provide some extra information for the item 
returned by the URI ``base_uri`` + ``relative_uri``.
The standard also defined some :doc:`templates <templates>`
for the data format. For example, we are using ``2dgameAsset`` for our Master Sword.

.. code-block:: js
  :caption: Response of http://dgoods.eosstudio.io/quickstart/weapons/sword/master_sword 

  {
    "type": "2dgameAsset",
    "name": "Master Sword",
    "description": "Master Sword is",
    "imageSmall": "http://dgoods.eosstudio.io/quickstart/weapons/sword/pic/master_sword_sm.jpg", // 150 x 150
    "imageLarge": "http://dgoods.eosstudio.io/quickstart/weapons/sword/pic/master_sword_lg.jpg", // 1024 x 1024
    "details": {
      "attack": 30
    },
    "authenticityImage": ""
  }

If you want to make sure the metadata is immutable,
you can also use URIs pointing to data saved on a blockchain (such as IPFS).
The full URI is only fixed when the asset is *issued*.
In that case you can issue all tokens to one account to lock down 
the metadata, 
and transfer to player accounts or list them through the exchange later. 

We can also use :cpp:func:`issue` action to issue some fungible tokens

.. code-block:: js

  // Execute action *issue* with parameters
  to: "demonhunter"
  category: "currencies"
  token_name: "gold"
  quantity: "100.0000 GOLD"
  relative_uri: "" // will not be used for fungible tokens
  memo: "Take this to start your advanture."

Let's explore what this data looks like on chain so far:


- Table ``dgoodstats`` with scope ``weapons``

============  ============  ============  ============  ============  ============  ========================  ============  ========================  ========================  ============  ============  ========================================================================
fungible      burnable      sellable      transferable  issuer        token_name    category_name_id          max_supply    current_supply            issued_supply             rev_partner   rev_split     base_uri
============  ============  ============  ============  ============  ============  ========================  ============  ========================  ========================  ============  ============  ========================================================================
false         true          true          true          diablo.dapp   sword         0                         10000 SWORD   1 SWORD                   1 SWORD                   diablo.dapp   0.05          http://dgoods.eosstudio.io/quickstart/weapons/sword/
============  ============  ============  ============  ============  ============  ========================  ============  ========================  ========================  ============  ============  ========================================================================


- Table ``dgoodstats`` with scope ``currencies``

============  ============  ============  ============  ============  ============  ========================  ========================  ========================  ========================  ============  ============  ========================================================================
fungible      burnable      sellable      transferable  issuer        token_name    category_name_id          max_supply                current_supply            issued_supply             rev_partner   rev_split     base_uri
============  ============  ============  ============  ============  ============  ========================  ========================  ========================  ========================  ============  ============  ========================================================================
true          true          false         true          diablo.dapp   gold          1                         1000000000.0000 GOLD      100.0000 GOLD             100.0000 GOLD             diablo.dapp   0             http://dgoods.eosstudio.io/quickstart/currencies/gold/
============  ============  ============  ============  ============  ============  ========================  ========================  ========================  ========================  ============  ============  ========================================================================


- Table ``dgood`` with scope ``diablo.dapp``

============  ========================  ============  ============  ============  ============
id            serial_number             owner         category      token_name    relative_uri
============  ========================  ============  ============  ============  ============
true          true                      demonhunter   weapons       sword         master_sword
============  ========================  ============  ============  ============  ============


- Table ``accounts`` with scope ``demonhunter``

========================  ============  ============  ========================
category_name_id          category      token_name    amount
========================  ============  ============  ========================
0                         weapons       sword         1 SWORD
1                         currencies    gold          100.0000 GOLD
========================  ============  ============  ========================


-------------------------------------------
Transfer
-------------------------------------------

The owner of NFTs can transfer them to another account using the :cpp:func:`transfernft` action.

.. code-block:: js

  // Execute action *transfernft* with parameters
  from: "demonhunter"
  to: "necromancer"
  dgood_ids: [0]
  memo: "Take the weapons and fight with me."

Notice that the parameter ``dgood_ids`` is an array which means you can transfer multiple items
in a single action call. The id's are coming from the :cpp:var:`dgood` table and make sure you have 
ownership of all assets being transfered.

NFTs can also be sold in the built-in :ref:`decentralized exchange <Decentralized Exchange>`
which we will talk about in later sections.

To transfer fungible tokens, use :cpp:func:`transferft`

.. code-block:: js

  // Execute action *transferft* with parameters
  from: "demonhunter"
  to: "necromancer"
  category: "currencies"
  token_name: "gold"
  quantity: "10.0000 GOLD"
  memo: "Take the sword and fight with me."

Fungible tokens cannot be sold in the built-in exchange.

-------------------------------------------
Burn
-------------------------------------------

If the token is ``burnable``, the owner can permanently destroy
it using :cpp:func:`burnnft` (for NFTs) or :cpp:func:`burnft` (for fungible tokens)

.. code-block:: js

  // Execute action *burnft* with parameters
  owner: "necromancer"
  dgood_ids: [0]
  memo: "Destroy the sword"

.. code-block:: js

  // Execute action *burft* with parameters
  owner: "necromancer"
  category_name_id: "1"
  quantity: "1.0000 GOLD"

You can find the ``category_name_id`` either in the :cpp:var:`dgoodstats` table
or the :cpp:var:`accounts` table.


.. rubric:: Next Step

We have covered the process of creating, issuing, transfering and burning some
digital assets. In the next setion, we will talk about how to look at your assets in 
3rd-party wallets.