Smart Contract (exchange)
===========================================

The dGoods standard also has a built in exchange.


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

Table corresponding to tokens on sale and therefore locked to disable transfer.

.. code-block:: cpp

  // scope is self
  TABLE lockednfts {
    uint64_t dgood_id;

    uint64_t primary_key() const { return dgood_id; }
  };
