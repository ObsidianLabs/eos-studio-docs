Smart Contract (assets)
===========================================

.. note::

  This section will explain the dGoods smart contract, including
  actions, tables, and metadata templates.

In the following sections we describe the minimum set of required 
methods and functionality for implementing this specification, along with 
the table structure, and finally some example metadata templates.


-------------------------------------------
Tables
-------------------------------------------

tokenconfigs
-------------------------------------------

The first table created and must be initialized 
with ``setconfig`` before any tokens can be created.

This is a singleton which holds the some basic information for the contract, including

- ``symbol`` for the contract;
- ``standard`` and ``version`` that let wallets know what they need to support for this contract;
- ``category_name_id`` which is like a global id for ``category:token_name`` pairs.
  The value will increment by one every time a new type of token is created by ``create`` action.

.. code-block:: cpp

  // scope is self
  TABLE tokenconfigs {
    eosio::symbol_code symbol;
    eosio::name standard;
    string version;
    uint64_t category_name_id;
  };


dgoodstats
-------------------------------------------

Stores token info such as whether it is fungible, burnable, or transferrable, 
and what the current and max supplies are. Info is written when a token 
is created.

The ``category`` is used as table ``scope`` and ``token_name``
is the primary key, so it ensures each ``category:token_name`` pair is
unique.

.. code-block:: cpp

  // scope is category, then token_name is unique
  TABLE dgoodstats {
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
  };


categoryinfo
-------------------------------------------

Holds all category names for easy querying.

.. code-block:: cpp

  // scope is self
  TABLE categoryinfo {
    eosio::name category;

    uint64_t primary_key() const { return category.value; }
  };


dgood
-------------------------------------------

The global list for non or semi-fungible tokens.
Secondary indices are used to search by ``owner``.

.. code-block:: cpp

  // scope is self
  TABLE dgood {
    uint64_t id;
    uint64_t serial_number;
    eosio::name owner;
    eosio::name category;
    eosio::name token_name;
    std::optional<string> relative_uri;

    uint64_t primary_key() const { return id; }
    uint64_t get_owner() const { return owner.value; }
  };


accounts
-------------------------------------------

Holds account information, including balances for fungible tokens  
and numbers of NFTs that account owns of a given type.

.. code-block:: cpp

  // scope is owner
  TABLE accounts {
    uint64_t category_name_id;
    eosio::name category;
    eosio::name token_name;
    eosio::asset amount;

    uint64_t primary_key() const { return category_name_id; }
  };


-------------------------------------------
Actions
-------------------------------------------

setconfig
-------------------------------------------

.. cpp:function:: ACTION dgoods::setconfig(eosio::symbol_code sym, string version)

Adds the symbol and version of dgoods spec.
It also initializes the ``category_name_id`` to zero.
Must be called first. Can be called again to update the version,
but the symbol will not update.

create
-------------------------------------------

.. cpp:function:: ACTION dgoods::create(eosio::name issuer, eosio::name category, eosio::name token_name, bool fungible, bool burnable, bool transferable, string base_uri, string max_supply)

Instantiates a token. This is required before any tokens can be issued 
and sets properties such as the category, name, maximum supply, rev split,
who has the ability to issue tokens, and if the token is fungible or not etc. 
Name type is a string 12 characters max a-z, 1-5. Max supply is given as an 
eosio asset. For non fungible tokens, precision must be 0 (you must use ints).
The symbol in the asset must match the symbol in ``setconfig``.


issue
-------------------------------------------

.. cpp:function:: ACTION dgoods::issue(eosio::name to, eosio::name category, eosio::name token_name, string quantity, string relative_uri, string memo)

Mints a token and gives ownership to the ``to`` account name. 
For a valid call the ``category``, and ``token_name`` must have 
been first created. Quantity will be set to 1 if non-fungible or semi-fungible,
otherwise quantity must match precision of ``max_supply``.
``Metadata_type`` must be one of the accepted metadata type templates.


transferft
-------------------------------------------

.. cpp:function:: ACTION dgoods::transferft(eosio::name from, eosio::name to, eosio::name category, eosio::name token_name, eosio::asset quantity, string memo)

Transfer fungible tokens ``category:token_name``.

transfernft
-------------------------------------------

.. cpp:function:: ACTION dgoods::transfernft(eosio::name from, eosio::name to, vector<uint64_t> dgood_ids, string memo)

Transfer non-fungible tokens.


burnft
-------------------------------------------

.. cpp:function:: ACTION dgoods::transferft(eosio::name owner, uint64_t category_name_id, asset quantity)

Destroys fungible tokens and frees the RAM if all are deleted from an account. 
``quantity`` must match precision of ``max_supply``. Only owner may call Burn function 
and burnable must be true.

burnnft
-------------------------------------------

.. cpp:function:: ACTION dgoods::transfernft(eosio::name owner, vector<uint64_t> dgood_ids)

Destroys specified tokens and frees the RAM. Only owner may call burn function, 
burnable must be true, and token must not be locked.


-------------------------------------------
Metadata Templates
-------------------------------------------

In order for wallets or dApps to support various digital goods, 
there need to be standards associated with the metadata. Our 
approach is to define templates based on the type of good. The 
following templates are candidates we have put forth, but this 
is to be a collaborative exercise. We want to provide a repository 
of templates that are agreed upon by the community. All metadata is 
formatted as JSON objects specified from the template types.

3dgameAsset
-------------------------------------------

2dgameAsset
-------------------------------------------

ticket
-------------------------------------------

art
-------------------------------------------

jewelry
-------------------------------------------
