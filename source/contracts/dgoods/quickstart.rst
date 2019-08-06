===========================================
Quickstart
===========================================

Before dive into details, we will first go through the key steps of dGoods standard 
and learn how to use it to issue some digital assets. For those who only need to use dGoods in 
some basic scenarios, this section should be enough for you to understand some common practices of 
dGoods.

We will use EOS Studio Web and EOS Studio Cloud-hosted Network in this tutorial
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
  For easy reference, we will call it ``botw.game``

- Add ``eosio.code`` permission to ``botw.game@active``

- Create two accounts ``link`` and ``zelda`` as players in the game

Initialization
===========================================

Each dGoods contract has a universal ``symbol`` to govern all digital assets created under it.
The ``symbol`` can be any strings of capital letters up to 7 characters.
To initialize a new dGoods contract, it's required to first execute the ``setconfig`` action 
to set the ``symbol`` along with some other configs.
Open ``botw.game`` contract in EOS Studio's :ref:`Contract <es-contract>` page and 
execute the ``setconfig`` action with

.. code-block:: js

  // Execute action *setconfig* with parameters
  symbol: "BOTW"
  version: "1.0"

Lifecycle of digital assets
===========================================

Digital assets usually go through the following process to be created and 
then circulated among different owners.

- When a type of asset is *created*, the basic properties are locked-in on the blockchain;
  that include the max supply and whether the asset is fungible, transferable, sellable or burnable
- Created assets can be *issued* to some accounts as initial owners
- The owner of an asset can *transfer* or *sell* it to another account
- *Burnable* assets can be permanently destroyed (usually) by the owner

-------------------------------------------
Create
-------------------------------------------

The dGoods contract use a two level structure ``category:token_name`` to classify its assets.
For example, if we want to create *sword* items for our game, we can use ``category=weapons`` 
and ``token_name=sword``. Each sword is unique because they have different attributes, 
and this type of asset is called *non-fungible token (NFT)*.

.. Now that we have set the tokenconfig data, we are free to create and issue tokens.

Execute the ``create`` action

.. code-block:: js

  // Execute action *create* with parameters
  issuer: "botw.game"
  rev_partner: "botw.game"
  category: "weapons"
  token_name: "sword"
  fungible: false
  burnable: true // can be burned or destroyed
  sellable: false // cannot be sold
  transferable: true // can be transfered
  rev_split: 0
  base_uri: "https://botw.game/weapons/sword/"
  max_supply: "10000 SWORD"

Here we created a type of asset with a max supply of 10000 swords.

- The ``base_uri`` will together with ``relative_uri`` in each item to provide extra information,
  preferably one of the predefined templates.
- If the asset is ``sellable``, ``rev_partner`` and ``rev_split`` are used together to distribute
  the income from selling the asset.
  The ``rev_partner`` (in this case specified to the contract itself) 
  will get ``rev_split`` of the sold amount with the seller getting the remainings.

We will use the ``create`` action again to define an in-game currency.

.. code-block:: js

  // Execute action *create* with parameters
  issuer: "botw.game"
  rev_partner: "botw.game"
  category: "currencies"
  token_name: "gold"
  fungible: true
  burnable: false
  sellable: false
  transferable: true
  rev_split: 0
  base_uri: "https://botw.game/currencies/gold/"
  max_supply: "1000000000.0000 GOLD"

This type of asset is called *fungible tokens*, which means each unit (each ``GOLD``) is identical.

Note that for both fungible and non-fungible assets, ``max_supply`` is always of type ``asset`` 
which carries a symbol and a precision. For NFTs the precision must be an integer.

-------------------------------------------
Issue
-------------------------------------------

One possibility is to issue all of the tokens to one of our accounts and then list it 
for sale through the marketplace. The other option is to issue directly to consumers 
who bought a ticket through a normal payment processor.
Either way issuing is done as follows:

``issue`` an NFT

.. code-block:: js

  // Execute action *issue* with parameters
  to: "link"
  category: "weapons"
  token_name: "sword"
  quantity: "1 SWORD"
  relative_uri: "master_sword.json"
  memo: "You justed picked the Master Sword!"

.. todo::

  Explain how ``https://botw.game/weapons/sword/master_sword.json`` is used.
  Make sure it returns a json response.

Or ``issue`` some fungible tokens

.. code-block:: js

  // Execute action *issue* with parameters
  to: "link"
  category: "currencies"
  token_name: "gold"
  quantity: "100.0000 GOLD"
  relative_uri: "" // is this required?
  memo: "Take this to start your advanture."

.. todo::

  How to lock in specific attributes for each asset?

If we wanted to use ipfs to store the metadata we should issue one token at a 
time and put the ipfs hash in relative_uri and the metadata for this token would be 
``base_uri`` + ``relative_uri``

Let's explore what this data looks like on chain so far:

.. todo::

  How to look at current data

-------------------------------------------
Transfer
-------------------------------------------

The owner of NFTs can transfer it to another account using ``transfernft`` action
and specifying the tokens to transfer by their ``dgood_id``.

.. code-block:: js

  // Execute action *transfernft* with parameters
  from: "link"
  to: "zelda"
  dgood_ids: [0, 1]
  memo: "Take the weapons and fight together with me."

To transfer some fungible tokens

.. code-block:: js

  // Execute action *transferft* with parameters
  from: "link"
  to: "zelda"
  category: "currencies"
  token_name: "gold"
  quantity: "10.0000 GOLD"
  memo: "Take the sword and fight together with me."


-------------------------------------------
Burn
-------------------------------------------

Burn an NFT

.. code-block:: js

  // Execute action *burnnft* with parameters
  owner: "zelda"
  dgood_ids: [0]
  memo: "xxx xxx"

Burn fungible tokens

.. code-block:: js

  // Execute action *burnft* with parameters
  owner: "zelda"
  category_name_id: "xxx" // how to find it?
  memo: "xxx xxx"


View digital assets in wallets
===========================================

- View the digital asset in EOS wallets (e.g. TokenPocket).

.. note::

  The goal of this section is to give readers a perliminary understanding of dGoods.
  For some dApp developers who just want to use dGoods for some very 
  simple tasks (e.g. just issue some tickets), they should know how do that
  after this section. People who want to learn more can continue to the
  following sections where we will elaborate on details.