===========================================
Quickstart
===========================================

.. note::

  Before dive into the details of dGoods standard, I think it's
  helpful to briefly demonstrate the basic workflow of issuing 
  a digital asset using dGoods. We will use EOS Studio Web for
  the demonstration so users don't need to set up a development
  environment.

Preparations
===========================================

- Open dGoods smart contract at https://app.eosstudio.io/eosstudio/dgoods 
  in EOS Studio Web. (will change to https://app.eosstudio.io/mythicalgames/dgoods)

- Create a new account on EOS Studio Cloud-hosted Network 

- Deploy the dGoods smart contract

Initialization
===========================================

- Each dGoods contract will hold a ``symbol``.
- Call ``setconfig``.

Create digital assets
===========================================

- Create a NFT using a metadata template
- Issue the NFT with some information
- Create & issue a fungible token
- Create & issue a semi-fungible token

Transfer digital assets
===========================================

- Transfer the NFT using ``transfernft``
- Transfer the fungible token using ``transferft``
- Transfer the semi-fungible token

View digital assets in wallets
===========================================

- View the digital asset in EOS wallets (e.g. TokenPocket).

.. todo::

  We probably also need to explain ``category`` somewhere.

.. note::

  The goal of this section is to give readers a perliminary understanding of dGoods.
  For some dApp developers who just want to use dGoods for some very 
  simple tasks (e.g. just issue some tickets), they should know how do that
  after this section. People who want to learn more can continue to the
  following sections where we will elaborate on details.