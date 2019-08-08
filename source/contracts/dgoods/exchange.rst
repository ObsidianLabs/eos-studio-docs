===========================================
Smart Contract (exchange)
===========================================

The dGoods standard also has a built in exchange.


Actions
===========================================


.. cpp:class:: dgoods

  .. cpp:function:: ACTION listsalenft(eosio::name seller, vector<uint64_t> dgood_ids, eosio::asset net_sale_amount)

  Used to list nfts for sale in the token contract itself. Callable only by ``owner``, 
  if sellable is true and token not locked, creates sale listing in the token contract, 
  marks token as not transferable while listed for sale. An array of ``dgood_ids`` is required.


  .. cpp:function:: ACTION closesalenft(eosio::name seller, uint64_t batch_id)

  Callable by seller if listing hasn't expired, or anyone if the listing is expired; 
  will remove listing, remove lock and return nft to seller


  .. .. cpp:function:: ACTION buynft(eosio::name from, eosio::name to, eosio::asset quantity, string memo)


Tables
===========================================

.. cpp:class:: dgoods

  .. cpp:var:: TABLE asks

  .. code-block:: cpp

    // scope is self
    uint64_t batch_id;
    vector<uint64_t> dgood_ids;
    eosio::name seller;
    eosio::asset amount;
    time_point_sec expiration;

    uint64_t primary_key() const { return batch_id; }
    uint64_t get_seller() const { return seller.value; }

  Holds listings for sale in the built in decentralized exchange (DEX)


  .. cpp:var:: TABLE lockednfts

  .. code-block:: cpp

    // scope is self
    uint64_t dgood_id;

    uint64_t primary_key() const { return dgood_id; }

  Table corresponding to tokens on sale and therefore locked to disable transfer.
