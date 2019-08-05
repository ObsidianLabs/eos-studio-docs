Smart Contract
===========================================

.. note::

  This section will explain the dGoods smart contract, including
  actions, tables, and metadata templates.

In the following sections we describe the minimum set of required 
methods and functionality for implementing this specification, along with 
the table structure, and finally some example metadata templates.

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


-------------------------------------------
Tables
-------------------------------------------

Token Config Table
-------------------------------------------

Singleton which holds the symbol for the contract, version, 
type of standard, and ``category_name_id``, which is like a global 
id for category and token name pairs. This is the first table 
created and must be defined with ``setconfig`` before any tokens 
can be created. As part of a "standard among standards" the 
token configs table will have a version and standard field that 
lets wallets know what they will need to support from this contract.

.. code-block:: cpp

  // scope is self
  TABLE tokenconfigs {
    eosio::name standard;
    string version;
    eosio::symbol_code symbol;
    uint64_t category_name_id;
  };


dGood Stats Table
-------------------------------------------

Ensures there can be only one ``category:token_name`` pair. 
Stores whether a given token is fungible, burnable, or transferrable 
and what the current and max supplies are. Info is written when a token 
is created. Issued supply is needed to keep track of unique id's when 
tokens are burned as issued supply never decreases.


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
    eosio::asset issued_supply;
    double rev_split;
    string base_uri;

    uint64_t primary_key() const { return token_name.value; }
  };


dGood Table
-------------------------------------------

This is the global list of non or semi-fungible tokens. Secondary 
indices provide search by ``owner``.

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


Category Table
-------------------------------------------

Holds all category names for easy querying.

.. code-block:: cpp

  // scope is self
  TABLE categoryinfo {
    eosio::name category;

    uint64_t primary_key() const { return category.value; }
  };

Asks Table
-------------------------------------------

Holds listings for sale in the built in decentralized exchange (DEX)

.. code-block:: cpp

  // scope is self
  TABLE asks {
    uint64_t batch_id;
    vector<uint64_t> dgood_ids;
    eosio::name seller;
    eosio::asset amount;
    time_point_sec expiration;

    uint64_t primary_key() const { return batch_id; }
    uint64_t get_seller() const { return seller.value; }
  };

Locked NFT Table
-------------------------------------------

Table corresponding to tokens that are locked and temporarily not transferable

.. code-block:: cpp

  // scope is self
  TABLE lockednfts {
    uint64_t dgood_id;

    uint64_t primary_key() const { return dgood_id; }
  };

Account Table
-------------------------------------------

The Account table holds the fungible tokens for an account, 
and a reference to how many NFTs that account owns of a given type.

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
