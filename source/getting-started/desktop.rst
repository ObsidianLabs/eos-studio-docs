===========================================
EOS Studio Desktop
===========================================

EOS Studio Desktop is a stand-alone desktop appliction, 
supporting Mac OS, Windows and Linux. 

Download
===========================================

You can download the installation package from the following links:

- **Mac OS**: https://download.eosstudio.io/mac
- **Windows**: https://download.eosstudio.io/win
- **Linux**: https://download.eosstudio.io/linux

Set up the environment
===========================================

When you launch EOS Studio for the first time, there will be a welcome screen
to help you set up the tools required for EOSIO dApp development.
That includes:

- `EOSIO <https://github.com/EOSIO/eos>`_ is the main software to run a EOSIO-based
  blockchain. It includes

  - ``nodeos``: the core executable that runs the EOSIO blockchain
    for block production and providing API endpoints. You need to start one
    for local development;

  - ``cleos``: a command line tool to query the EOSIO blockchain;

  - ``keosd``: a commane line wallet to manage keypairs and sign transactions;

- `EOSIO.CDT <https://github.com/EOSIO/eosio.cdt>`_ which stands for *Contract Development Toolkit*
  is used to compile C++ source codes to
  `WebAssembly <https://developers.eos.io/eosio-home/docs#section-c-wasm-virtual-machine>`_,
  a binary format EOSIO uses to run smart contracts.

EOS Studio Desktop uses `Docker <https://www.docker.com>`_ to install and 
run the above tools. With dockerized EOSIO and EOSIO.CDT, it's easier to work
accross differenct operating systems.
If you don't have Docker yet, the welcome page will guide you 
to install it.

In EOS Studio, ``cleos`` and ``keosd`` are not necessary.

.. note::

    **[Windows]** There are two types of Docker:
    `Docker Desktop <https://www.docker.com/products/docker-desktop>`_
    (for Window 10 Pro only) and 
    `Docker Toolbox <https://docs.docker.com/toolbox/toolbox_install_windows/>`_
    (for all the others). Be sure to know which type of Docker your are using. 
    EOS Studio works better with Docker Desktop, but it has some compatible issues
    with Docker Toolbox. In that case, we recommend to use :ref:`cloud CDT` and 
    Cloud-hosted Network.

.. note::

    **[Linux]** After install docker, you also need to 
    allow non-privileged users to run Docker commands.
    See instructions `here <https://docs.docker.com/install/linux/linux-postinstall/>`_.

After Docker is installed and launched, the welcome page will further assist you 
to download docker images for
EOSIO (`eostudio/eos <https://hub.docker.com/r/eostudio/eos>`_) and 
EOSIO.CDT (`eostudio/eosio.cdt <https://hub.docker.com/r/eostudio/eosio.cdt>`_).
Both of them have many versions, but in most cases you only need to
install the latest. If you have previous projects that only work with 
older EOSIO or EOSIO.CDT, you can download multiple versions and EOS Studio 
will help you to manage them.

.. warning::

    Do not use the docker image on mainnet or as a block producer. They are made 
    for development purpose only.


Create a new project
===========================================

Once you finish the installations, EOS Studio will go to
the page of your project list. It is empty now, so let's 
click the `Create` button and create a new project.

The new project will be initilized with some basic codes for a smart contract.
You can now press the :fa:`gavel` `hammer` button in the `toolbar` to build the project.
This will run the EOSIO.CDT docker image to compile the contract and
export a ``.wasm`` file and an ``.abi`` file.

- The ``wasm`` file is a WebAssembly binary that will run on the EOSIO blockchain

- The ``abi`` file is a json object that defines the contract actions 
  and data tables with type information of action parameters and table rows.

Start a local network
===========================================

Before going forward, you need to start a local network. Switch to the Network Page
where you can see all installed EOISO softwares. Click the `Run` button to start
a network. EOS Studio will 


Create an account
===========================================


Deploy the contract
===========================================

.. warning::

    Be careful that do not deploy to ``eosio`` account, unless you know what you
    are doing.

Execute the contract action
===========================================

