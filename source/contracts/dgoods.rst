===========================================
dGoods
===========================================

`dGoods <https://github.com/MythicalGames/dgoods>`_ is an open source and free 
standard for handling the virtual representation of items, both digital and 
physical, on the EOS blockchain led by `Mythical Games <https://mythical.games/>`_. 
Fungible and non-fungible tokens are represented in this standard, 
as well as what we term semi-fungible tokens. These are nft's that 
share many properties but have at minimum a unique serial number. 
Things like tickets, luxury goods, or even fractionalized ownership 
are well suited to this classification and therefore, a major focus 
of this standard.

Three most important properties of dGoods which differentiate from other token standards

- hierarchical naming structure of category:name for each token or set of tokens, enabling filtering and organization of tokens

- focus on semi-fungible tokens -- that is, tokens that are 1 of n with a serial number or slightly different metadata

- opensource metadata types and standardization for each type with localization, eg ``3dGameAsset``, ``Ticket``, etc


Introduction
===========================================

Frontend
===========================================

Smart Contract
===========================================

See here https://app.eosstudio.io/eosstudio/dgoods

-------------------------------------------
Actions
-------------------------------------------

setconfig
-------------------------------------------

Set config adds the symbol, and version of dgoods spec.
It also initializes the category_name_id to zero.
Must be called first. Can be called again to update the version,
but the symbol will not update.

.. cpp:function:: ACTION dgoods::setconfig(const eosio::symbol_code &sym, string version)


create
-------------------------------------------

Set config adds the symbol, and version of dgoods spec.
It also initializes the ``category_name_id`` to zero.
Must be called first. Can be called again to update the version,
but the symbol will not update.

.. cpp:function:: ACTION dgoods::create(const eosio::name &issuer, const eosio::name &category, const eosio::name &token_name, bool fungible, bool burnable, bool transferable, string base_uri, string max_supply)


issue
-------------------------------------------

The issue method mints a token and gives ownership to the 'to' 
account name. For a valid call the ``category``, and ``token_name`` must have 
been first created. Quantity will be set to 1 if non-fungible or semi-fungible,
otherwise quantity must match precision of ``max_supply``.
``Metadata_type`` must be one of the accepted metadata type templates.

.. cpp:function:: ACTION dgoods::issue(const eosio::name &to, const eosio::name &category, const eosio::name &token_name, string quantity, string relative_uri, string memo)
