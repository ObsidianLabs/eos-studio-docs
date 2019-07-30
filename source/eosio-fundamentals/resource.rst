===========================================
Resource Model
===========================================

The EOSIO blockchain defines three types of resources to
compensate the usage of the network:

- CPU for computation time
- NET for network bandwidth
- RAM for data storage

Since these three resources are all limited, the EOSIO network
designed models to distribute them and ...

CPU
===========================================

账户在调用任何合约时，都会计算，消耗CPU。需要抵押一定量的EOS来换取CPU使用权。
网络将根据所有账户抵押的CPU总量，按比例分配每个账户可以使用的CPU time。这个时间
代表24 hours内可以使用的CPU总时长。一个账户需要使用的CPU越多，就需要

抵押的EOS可以refund，但是需要等待72 hours.

NET
===========================================

NET的distribution机制和CPU一样，也是通过抵押的方式，
来分配network bandwidth的使用权。

RAM
===========================================

链上的存储空间总量是有限的，所以存储数据需要购买RAM。
RAM需要购买，并且通过bancor算法来自动调节价格。
买的人越多，RAM价格越高，and vice versa.

REX
===========================================

If an account need to use a large amount of CPU or NET for
a short period of time, it needs to stake a huge amount of EOS token.
 