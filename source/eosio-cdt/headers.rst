===========================================
Header Files
===========================================

symbol.hpp
===========================================

.. code-block:: cpp

  #include <eosio/symbol.hpp>

.. cpp:class:: eosio::symbol_code

  Information about a token symbol, the symbol can be up to 7 characters long.

  Example:

  .. code-block:: cpp

    auto symbol_code = eosio::symbol_code("EOS");

  .. cpp:function:: symbol_code(std::string_view str)

    Construct a new ``symbol_code`` initialising value with ``str``

  .. cpp:function:: symbol_code(uint64_t raw)

    Construct a new ``symbol_code`` initialising value with ``raw``

  .. cpp:function:: std::string to_string()

    Returns the symbol name as a string
  
  .. cpp:function:: uint64_t raw()

    Returns the raw ``uint64_t`` value for the symbol
  
  .. cpp:function:: friend bool operator == (const symbol_code& a, const symbol_code& b)
  .. cpp:function:: friend bool operator != (const symbol_code& a, const symbol_code& b)
  .. cpp:function:: friend bool operator < (const symbol_code& a, const symbol_code& b)

  .. cpp:var:: private uint64_t value

    Stores the symbol code as a ``uint64_t`` value

.. cpp:class:: eosio::symbol

  Used to define a token's :cpp:class:`symbol_code` and ``precision`` (digits after the decimal).
  
  Example:

  .. code-block:: cpp

    auto symbol = eosio::asset("10.0000 EOS").symbol;
    symbol.code(); // eosio::symbol_code("EOS")
    symbol.precision(); // 4

  For example, ``10.0000 EOS`` has *symbol_code* **EOS** and *precision* **4**.
  A ``symbol`` can be written as ``{precision},{symbol}``
  (in above example, ``4,EOS``).

  .. cpp:function:: symbol(eosio::symbol_code sc, uint8_t precision)
  .. cpp:function:: symbol(std::string_view sc, uint8_t precision)

  .. cpp:function:: eosio::symbol_code code()
  .. cpp:function:: uint8_t precision()


.. cpp:class:: eosio::extended_symbol

  A type of token is created by the
  :cpp:func:`token::create` action.
  The same contract account cannot ``create`` two types of tokens 
  with the same ``symbol``,
  but two different accounts deployed with the same ``eosio.token`` contract can
  create separate tokens with identical ``symbol``.

  To prevent such vulnerability,
  a ``extended_symbol`` s could be equal but represent two different tokens.

  .. cpp:member:: int64_t amount = 0

  .. cpp:member:: eosio::symbol symbol



asset.hpp
===========================================

.. code-block:: cpp

  #include <eosio/asset.hpp>

.. cpp:class:: eosio::asset

  Used to specify some amount of tokens. 
  It consists of an ``amount`` property and a ``symbol`` property.
  For example, ``10.0000 EOS`` is an ``asset`` with 
  ``amount`` equals ``10 * 10^4`` and ``symbol`` equals ``4,EOS``. 


name.hpp
===========================================

.. code-block:: cpp

  #include <eosio/name.hpp>

.. cpp:struct:: eosio::name
  
  Mainly used to represent an eosio account name.
  Name string can only have small letters a-z, digits 1-5 or dot, and max 12 characters.
  The name is saved as a ``uint64_t``.

  .. cpp:function:: name(std::string_view str)

    Construct a new ``name`` initialising value with ``str``

  .. cpp:function:: name(uint64_t raw)

    Construct a new ``name`` initialising value with ``raw``

  .. cpp:function:: std::string to_string()

    Returns the name as a string
  
  .. cpp:function:: uint64_t raw()

    Returns the raw ``uint64_t`` value for the name

  .. cpp:function:: friend bool operator == (const name& a, const name& b)
  .. cpp:function:: friend bool operator != (const name& a, const name& b)
  .. cpp:function:: friend bool operator < (const name& a, const name& b)

  .. cpp:var:: private uint64_t value

    Stores the name as a ``uint64_t`` value



time.hpp
===========================================

.. code-block:: cpp

  #include <eosio/time.hpp>

.. cpp:class:: eosio::microseconds

  Microseconds. 

  .. cpp:function:: microseconds(int64_t count = 0)
  .. cpp:function:: static microseconds maximum()
  
    Maximum ``0x7fffffffffffffffll``

  .. cpp:function:: int64_t count()
  .. cpp:function:: int64_t to_seconds()

  .. cpp:var:: int64_t _count

    The value used in serilization

.. cpp:function:: inline microseconds eosio::milliseconds( int64_t ms )
.. cpp:function:: inline microseconds eosio::seconds( int64_t s )
.. cpp:function:: inline microseconds eosio::minutes( int64_t m )
.. cpp:function:: inline microseconds eosio::hours( int64_t h )
.. cpp:function:: inline microseconds eosio::days( int64_t d )


.. cpp:class:: eosio::time_point

  High resolution time point in microseconds. 

  .. cpp:function:: time_point(microseconds elapsed = microseconds())


  .. cpp:function:: microseconds& time_since_epoch()
  .. cpp:function:: uint32_t sec_since_epoch()

  .. cpp:var:: microseconds elapsed
    
    The value used in serilization



.. cpp:class:: eosio::time_point_sec

  A lower resolution time_point accurate only to seconds from 1970. 

  .. cpp:function:: time_point_sec()
  .. cpp:function:: explicit time_point_sec(uint32_t seconds)
  .. cpp:function:: time_point_sec( const time_point& t )

  .. cpp:function:: time_point_sec maximum()

    Maximum ``time_point_sec(0xffffffff)``

  .. cpp:function:: time_point_sec min()

    Minimum ``time_point_sec(0)``

  .. cpp:function:: uint32_t sec_since_epoch()

    Returns :cpp:var:`utc_seconds`

  .. cpp:var:: uint32_t utc_seconds

    The value used in serilization

.. cpp:class:: eosio::block_timestamp
