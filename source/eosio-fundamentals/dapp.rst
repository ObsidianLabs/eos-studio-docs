===========================================
Decentralized Application
===========================================

EOSIO blockchain use account system to 


Kickoff
===========================================

You are three easy steps away from being a blockchain developer:

1. EOS Studio IDE and EOS account preparation

  - Launch EOS Studio IDE Web: https://app.eosstudio.io

  - Login into Studio using Github account

  - Create an EOS account in Cloud network

2. Backend preparation

  - Create a project

  - Build

  - Deploy

3. Frontend preparation (TODO add a simple test page with EOSJS API call)

  - Launch test page

  - Fill the account

  - Click 'Hi' button


That's it, that's all, you are a blockchain developer now.

Talk is cheap. Show me the code.


Backend (Smart Contract)
===========================================

- using C++

- Header file (hpp) and source file (cpp)

hpp:

- [[eosio::contract]] syntax

- Constructor and member initializer lists (https://en.cppreference.com/w/cpp/language/initializer_list)

- Actions

- Define table and table instance

cpp:

- function implementation

- require_auth (maybe ignore it)

- multi_index get a record (get)

- multi_index add a new record (emplace)

- multi_index replace an existing record (modify)

- C++ callback function


Frontend (TODO)
===========================================

From 'Hello world!' to 'Hello xxx!' (xxx from blockchain) after clicking a button.

Offer a reset button.

- React setup

- EOSJS setup

- onClick binding

- EOSJS API fetch

- update state
