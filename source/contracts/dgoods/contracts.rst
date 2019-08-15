===========================================
Smart Contract (assets)
===========================================

We will now describe the set of actions 
and table structures to implement the dGoods standard. In this section
we focus on assets-related actions and tables. In the next
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
      the value will increment by one every time :cpp:func:`create` is successfully executed


  .. cpp:var:: TABLE dgoodstats

    .. code-block:: cpp

      // scope is category
      bool fungible;
      bool burnable;
      bool sellable;
      bool transferable;
      eosio::name issuer;
      eosio::name token_name; // primary key
      uint64_t category_name_id;
      eosio::asset max_supply;
      eosio::asset current_supply;
      eosio::asset issued_supply;
      eosio::name rev_partner;
      double rev_split;
      string base_uri;

    Saves token info such as

    - if the token is ``fungible``, ``burnable``, ``sellable`` and ``transferable``
    - the ``issuer`` account who is authorized to issue the token
    - ``category`` (as table scope) and ``token_name`` to determine token classfication;
      the pair ``category:token_name`` must be unique
    - ``category_name_id`` which is like a global id for ``category:token_name``
    - ``max_supply``, ``current_supply``, ``issued_supply`` given as :cpp:class:`eosio::asset`;
      for NFTs the precision must be integer; ``issued_supply`` is used to keep track of unique id’s
      when tokens are burned as it never decreases
    - ``rev_partner`` and ``rev_split`` which are used to determine how to split the income when 
      the token is sold in the built-in :ref:`Decentralized Exchange`
    - ``base_uri`` will be used together with ``relative_uri`` from table :cpp:var:`dgood`
      to provide extra metadata for the token, 
      usually as one of :doc:`metadata templates <templates>`

    Info is written when a token is created.
    The ``category`` is used as table scope and ``token_name``
    is the primary key, so it ensures each ``category:token_name`` pair is
    unique.


  .. cpp:var:: TABLE categoryinfo

    .. code-block:: cpp

      // scope is self
      eosio::name category; // primary key

    Holds all category names for easy querying.


  .. cpp:var:: TABLE dgood

    .. code-block:: cpp

      // scope is self
      uint64_t id; // primary key
      uint64_t serial_number;
      eosio::name owner; // secondary key
      eosio::name category;
      eosio::name token_name;
      std::optional<string> relative_uri;

    The global list for non and semi-fungible tokens. Fungible tokens 
    are not be saved in this table.
    Secondary indices are used to search by ``owner``.

    - ``relative_uri`` will be used together with ``base_uri`` from table :cpp:var:`dgoodstats`
      to provide extra metadata for the token, 
      usually as one of :doc:`metadata templates <templates>`

  .. cpp:var:: TABLE accounts

    .. code-block:: cpp

      // scope is owner
      uint64_t category_name_id; // primary key
      eosio::name category;
      eosio::name token_name;
      eosio::asset amount;

    Holds account information. For fungible tokens ``amount`` is the token balance while
    for NFTs it is the number of owned NFTs. Users need to query table :cpp:var:`dgood`
    to find information for each NFT they own.


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
    See table :cpp:var:`dgoodstats` of how properties are defined.


  .. cpp:function:: ACTION issue(eosio::name to, eosio::name category, eosio::name token_name, eosio::asset quantity, string relative_uri, string memo)

    Mints a token and gives ownership to the ``to`` account. 
    The token ``category:token_name`` must be created first. 
    ``quantity`` must match the symbol and precision of ``max_supply``.
    ``fixme`` For NFTs, can issue up to 100 at one time.


  .. cpp:function:: ACTION transferft(eosio::name from, eosio::name to, eosio::name category, eosio::name token_name, eosio::asset quantity, string memo)

    Transfer the fungible tokens ``category:token_name``.
    Only applicable if the token is ``transferable``.
    The ``quantity`` must match the symbol and precision of ``max_supply``.


  .. cpp:function:: ACTION transfernft(eosio::name from, eosio::name to, vector<uint64_t> dgood_ids, string memo)

    Transfer non-fungible tokens. 
    Only applicable	if the token is ``transferable`` and not be locked (see table :cpp:var:`lockednfts`).
    ``dgood_ids`` are from table :cpp:var:`dgood`.


  .. cpp:function:: ACTION burnft(eosio::name owner, uint64_t category_name_id, eosio::asset quantity)

    Destroys fungible tokens and frees the RAM if all tokens are deleted from the account. 
    Only applicable if the token is ``burnable``. Only the owner may call this action. 
    The ``quantity`` must match the symbol and precision of ``max_supply``.
    The ``category_name_id`` is from table :cpp:var:`dgoodstats`.


  .. cpp:function:: ACTION burnnft(eosio::name owner, vector<uint64_t> dgood_ids)

    Destroys specified non-fungible tokens and frees the RAM.
    Only applicable if the token is ``burnable`` and not be locked (see table :cpp:var:`lockednfts`). 
    Only the owner may call this action. 
    ``dgood_ids`` are from table :cpp:var:`dgood`.


  .. cpp:function:: ACTION pausexfer(bool pause)

    Pauses all transfers of all tokens. Only callable by the contract. 
    If pause is true, will pause. If pause is false will unpause transfers.
