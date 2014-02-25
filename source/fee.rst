交易费计算
===========================================

币龄

优先级

交易费

免费

0.9
------------------------------------
每千字节 0.00001btc(0.01mbtc)
https://github.com/bitcoin/bitcoin/pull/3305
 

0.8.6
------------------------------------

0.8.5
------------------------------------

一个交易可以被免费地发送，如果同时满足这些条件：
小于1,000字节。
所有输出大于等于0.01 BTC。
它的优先级是足够高

收费

每千字节 0.0001btc(0.1mbtc)

计算优先级

优先级就是币的sum(bestheight-height)/bytes

免费
  优先级> COIN*144/250

* 手续费 fee = Sum(TxIn) - Sum(TxOut)

默认交易费
1.可以修改，在conf...


交易费的演进:
------------------------------------

https://gist.github.com/gavinandresen/7670433

参考
------------------------------------

https://en.bitcoin.it/wiki/Transaction_fees

https://en.bitcoin.it/wiki/Free_transaction_relay_policy
 
http://bitcoin.stackexchange.com/questions/3400/what-is-the-exact-formula-for-calculating-transaction-fees

http://bitcoin.stackexchange.com/questions/7537/calculator-for-estimated-tx-fees

http://bitcoin.stackexchange.com/questions/1195/how-to-calculate-transaction-size-before-sending

http://bitcoinfees.com/

https://github.com/bitcoin/bitcoin/commits/master/src/wallet.cpp?page=7

增加交易费，如何加快交易速度
------------------------------
 
标准bitcoin网络在创建block的时候会把所有不在block交易按优先级排序，先放入优先级高的

交易被发送后，保存在矿工的内存中，矿工创建block再广播出去，达到6个confirms,认为交易成功

Q. 非标准bitcoin client，如矿池创建的block，不按规则创建，如何检查？
必须满足checkblock, checktranslaction,否则会被丢弃

