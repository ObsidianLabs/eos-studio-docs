===========================================
Overview
===========================================

EOS Studio comes with a simple and intuitive layout. 
The UI is divided into four pages, switchable through the navbar buttons.

- :doc:`Project Editor <project>` is the main interface to display your EOSIO project
  and provide a EOSIO-tailored editor to code, build and deploy your smart contracts.

- :doc:`Contract Inspector<project>` is a convenient tool to debug smart contracts.
  It allows you to easily execute contract actions and visualize the table data.

- :doc:`Account Viewer<project>` is a page to view account information and 
  perform account related operations.

- :doc:`Network Manager<project>` can help you switching between local network, 
  different testnets, and EOS Mainnet, as well as showing the information of 
  the selected network. You can also connect to a custom network by API endpoint.
  For local network, it also integrates the 
  :ref:`EOSIO Version Manager<EOSIO Version Manager>` to install and manage 
  multiple versions of the EOSIO software.
  


At the bottom, the :doc:`Bottom Bar <bottom>`
will provide easy access to other tools that might be useful during the development.
Tools listed on the left side are usually needed in many places, so they are fixed
in the Bottom Bar.

- The :ref:`Keypair Manager<Keypair Manager>` can help you to create, import 
  or export keypairs. Be aware that EOS Studio will not encrypt
  the private keys and don't use any of those in the mainnet.
- The :ref:`Scatter<Scatter>` button will show connection status to 
  Scatter Desktop. EOS Studio will use Scatter to sign transactions when
  you are working on the mainnet.

However, tools on the right will change according to the current
active page and different pages have their own available tools.
The following sections will introduce them by pages respectively.