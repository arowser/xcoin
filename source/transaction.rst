 
交易
===========================================

交易在kevin的blog(648.io)中有很详细的描述.
 
raw m-of-n where n<=3, p2sh, p2pkh, p2pk 

p2sh: Pay To Script Hash 
p2pkh: pay-to-pubkey-hash 
p2pk:  pay-to-pubkey
m-of-n:

standard transaction types
Pubkey
Pubkey hash (P2PH)
Script hash (P2SH)
Multisig
Null Data


比特币交易构成（一）

http://618.io/blog/2013/10/27/bitcoin-transaction-01/

比特币交易构成（二）

http://618.io/blog/2013/10/27/bitcoin-transaction-02/

Send coins to a 2-of-3 multisig, then spend them

https://gist.github.com/gavinandresen/3966071 

2-of-2 escrow example
http://people.xiph.org/~greg/escrowexample.txt

https://en.bitcoin.it/wiki/Atomic_cross-chain_trading

http://bitcoindev.us.to/en/developer-guide#transactions

multisig example

https://blockchain.info/tx/7b80040a18c70f279ac32c7032e69c46aff50ee76398df7babf6f9039ed2561d

P2SH Safe Addresses
https://www.belshe.com/2013/12/15/p2sh-safe-addresses/

交易的可锻性（transaction malleability）
由于现在大部分使用的签名算法都是基于OpenSSL的ECDSA（椭圆曲线数字签名），这个签名算法的一个问题就是，修改签名的某个字节能够使得签名依然校验成功，这样伪造签名之后交易依然能成功进行。

https://en.bitcoin.it/wiki/Transaction_Malleability

改进方法

https://gist.github.com/sipa/8907691

最近的 Mt.Gox 停止比特币提现是怎么回事？比特币安全性究竟怎样?

http://www.zhihu.com/question/22787360/answer/22658207

http://bitcoinhistory.net/Technical_Papers/ProgrammingBitcoinTransactionScripts.pdf
