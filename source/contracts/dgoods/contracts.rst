===========================================
Smart Contract (assets)
===========================================

We will now describe the set of actions 
and table structures to implement the dGoods standard. In this section
we focus on assets-related actions and tables, as well as some 
example templates to define the metadata for digital assets. In the next
section we will talk about dGoods' built-in decentralized exchange.


Tables
===========================================

.. cpp:class:: dgoods

  .. cpp:var:: TABLE tokenconfigs

  .. code-block:: cpp

    // scope is self
    eosio::symbol_code symbol;
    eosio::name standard;
    string version;
    uint64_t category_name_id;

  The first table created and must be initialized with :cpp:func:`setconfig` before any tokens 
  can be created.
  :cpp:var:`tokenconfigs` is a singleton which holds the basic information for the contract including

  - ``symbol`` for the contract
  - ``standard`` and ``version`` that let wallets know what they need to support for this contract
  - ``category_name_id`` which is like a global id for ``category:token_name`` pairs;
    the value will increment by one every time a new type of token is created


  .. cpp:var:: TABLE dgoodstats

  .. code-block:: cpp

    // scope is category, then token_name is unique
    bool fungible;
    bool burnable;
    bool sellable;
    bool transferable;
    eosio::name issuer;
    eosio::name rev_partner;
    eosio::name token_name;
    uint64_t category_name_id;
    eosio::asset max_supply;
    eosio::asset current_supply;
    eosio::asset issued_supply; // keep track of unique id’s when tokens are burned as this never decreases
    double rev_split;
    string base_uri;

    uint64_t primary_key() const { return token_name.value; }

  Token info such as whether it is fungible, burnable, sellable or transferrable, 
  and what the current and max supplies are. Info is written when a token 
  is created.
  The ``category`` is used as table ``scope`` and ``token_name``
  is the primary key, so it ensures each ``category:token_name`` pair is
  unique.

  .. cpp:var:: TABLE categoryinfo

  .. code-block:: cpp

    // scope is self
    eosio::name category;

    uint64_t primary_key() const { return category.value; }
  
  Holds all category names for easy querying.


  .. cpp:var:: TABLE dgood

  .. code-block:: cpp

    // scope is self
    uint64_t id;
    uint64_t serial_number;
    eosio::name owner;
    eosio::name category;
    eosio::name token_name;
    std::optional<string> relative_uri;

    uint64_t primary_key() const { return id; }
    uint64_t get_owner() const { return owner.value; }
  
  The global list for non and semi-fungible tokens. Fungible tokens 
  are not be saved in this table.
  Secondary indices are used to search by ``owner``.


  .. cpp:var:: TABLE accounts

  .. code-block:: cpp

    // scope is owner
    uint64_t category_name_id;
    eosio::name category;
    eosio::name token_name;
    eosio::asset amount;

    uint64_t primary_key() const { return category_name_id; }
    
  Holds account information. For fungible tokens ``amount`` is the token balance while
  for NFTs it is the number of owned NFTs. Users need to query the ``dgood``
  table to find information for each NFT they own.


Actions
===========================================

.. cpp:class:: dgoods

  .. cpp:function:: ACTION setconfig(eosio::symbol_code sym, string version)

  Must be called first to initialize table :cpp:var:`tokenconfigs` with 
  a symbol and version of dGoods spec. 
  It also initializes ``category_name_id`` to zero.
  Can be called again to update the version but the symbol will not change.

  
  .. cpp:function:: ACTION create(eosio::name issuer, eosio::name rev_partner, eosio::name category, eosio::name token_name, bool fungible, bool burnable, bool sellable, bool transferable, double rev_split, string base_uri, eosio::asset max_supply)

  Defines a type of token before any tokens can be issued. 
  The action sets token properties such as

  - the ``issuer`` account authorized to issue tokens
  - ``category`` and ``token_name`` to determine token classfication;
    the pair ``category:token_name`` must be unique
  - if the token is ``fungible``, ``burnable``, ``sellable`` and ``transferable``
  - ``max_supply`` given as an :cpp:class:`eosio::asset`; for NFTs the precision must be integer
  - ``rev_partner`` and ``rev_split`` which are used to determine xxxx when 
    the token is sold in the built-in exchange


  .. cpp:function:: ACTION issue(eosio::name to, eosio::name category, eosio::name token_name, eosio::asset quantity, string relative_uri, string memo)

  Mints a token and gives ownership to the ``to`` account. 
  The token ``category:token_name`` must be created first. 
  Quantity will be set to 1 if non-fungible or semi-fungible,
  otherwise quantity must match precision of ``max_supply``.
  ``Metadata_type`` must be one of the accepted metadata type templates.


  .. cpp:function:: ACTION transferft(eosio::name from, eosio::name to, eosio::name category, eosio::name token_name, eosio::asset quantity, string memo)

  Transfer fungible tokens of ``category:token_name``.


  .. cpp:function:: ACTION transfernft(eosio::name from, eosio::name to, vector<uint64_t> dgood_ids, string memo)

  Transfer non-fungible tokens.


  .. cpp:function:: ACTION burnft(eosio::name owner, uint64_t category_name_id, eosio::asset quantity)

  Destroys fungible tokens and frees the RAM if all are deleted from an account. 
  ``quantity`` must match precision of ``max_supply``. Only owner may call Burn function 
  and burnable must be true.


  .. cpp:function:: ACTION burnnft(eosio::name owner, vector<uint64_t> dgood_ids)

  Destroys specified tokens and frees the RAM. Only owner may call burn function, 
  burnable must be true, and token must not be locked.


  .. cpp:function:: ACTION pausexfer(bool pause)

  Pauses all transfers of all tokens. Only callable by the contract. 
  If pause is true, will pause. If pause is false will unpause transfers.


.. _dgoods-contract-templates


Metadata Templates
===========================================

In order for wallets or dApps to support various digital goods, 
there need to be standards associated with the metadata. Our 
approach is to define templates based on the type of good. The 
following templates are candidates we have put forth, but this 
is to be a collaborative exercise. We want to provide a repository 
of templates that are agreed upon by the community. All metadata is 
formatted as JSON objects specified from the template types.

3dgameAsset
-------------------------------------------

.. code-block:: js

  {
    // Required Fields
    "type": string; "3dgameAsset"
    "name": string; identifies the asset the token represents
    "description": string; short description of the asset the token represents
    "imageSmall": URI pointing to image resource size 150 x 150
    "imageLarge": URI pointing to image resource size 1024 x 1024
    "3drender": URI pointing to js webgl for rendering 3d object
    "details": Key Value pairs to render in a detail view, could be things like {"strength": 5}
    // Optional Fields
    "authenticityImage": URI pointing to resource with mime type image representing certificate of authenticity
  }

2dgameAsset
-------------------------------------------

.. code-block:: js

  {
    // Required Fields
    "type": string; "2dgameAsset"
    "name": string; identifies the asset the token represents
    "description": string; short description of the asset the token represents
    "imageSmall": URI pointing to image resource size 150 x 150
    "imageLarge": URI pointing to image resource size 1024 x 1024
    "details": Key Value pairs to render in a detail view, could be things like {"strength": 5}
    // Optional Fields
    "authenticityImage": URI pointing to resource with mime type image representing certificate of authenticity
  }

ticket
-------------------------------------------

art
-------------------------------------------

jewelry
-------------------------------------------
