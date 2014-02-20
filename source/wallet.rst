钱包
===========================================

钱包内容
---------------
钱包的文件名是wallet.dat, 钱包里存放了，比特币地址，密钥，软件配置，个交易记录，具体内容

**钱包存储位置**

不同OS的存储路径不一样:

- Windows: %APPDATA%\Bitcoin\
- Linux: ~/.bitcoin/
- Mac: ~/Library/Application Support/Bitcoin/
 
**钱包里的内容**

- name - an address book name "name" + "bitcoinAddr", string addressbook name
- tx - a transaction 已经完成的交易数据，包括发送和接收的
- acentry - an accounting entry
- key - a pair of public and private keys 地址的密钥对
- mkey - a master key
- ckey - an encrypted key 
- defaultkey - the default receiving address' key, 默认的key, 一个压缩的public key
- pool - 预先生成的密钥对 https://en.bitcoin.it/wiki/Key_pool, 默认是100，可以通过命令行或者conf改
- version - the version of the software that wrote this wallet
- minversion - the earliest version of the software that can read this wallet
- cscript - a script
- bestblock - a pointer to the end of the best blockchain seen, 最后一个block
  Since 0.3.21: information about the current best chain, to be able to rescan automatically when restoring from a backup.
- wkey - a private key (no longer used)
- setting - used to store user interface settings (no longer used, except to hold an invalid setting to stop pre-0.4.0 versions of the client which don't understand minversion from loading wallets that are too new for them)
- "purpose"     地址的的用途, 包括: receive,send,refund
- "keymeta"     key相关信息，版本，创建时间
- "orderposnext", 默认配置每次交易完后会更换bitcoin地址，这个用于记录更换次数，
 
参考信息

http://bitcoin.stackexchange.com/questions/3173/what-information-does-a-wallet-contain

http://bitcoin.stackexchange.com/questions/853/what-do-the-different-dat-files-contain/854#854

https://bitcointalk.org/index.php?topic=258770.0

https://github.com/jackjack-jj/pywallet/blob/master/pywallet.py#l1699

http://www.faqoverflow.com/bitcoin/3173.html

https://github.com/jackjack-jj/pywallet

比特币地址
------------------------------

Bitcoin地址实质上是一对公私钥（参考 ECDSA）当中的公钥 

在比特币的世界里，比特币地址（格式类似这样：1JwSSubhmg6iPtRjtyqhUYYH7bZg3Lfy1T）相当于银行帐号，比特币私钥256位就是32字节（格式类似这样：5KJvsngHeMpm884wtkJNzQGaCErckhHJBGFsvd3VyK5qMZXj3hS）相当于开启这个帐号的密码，一个地址（帐号）对应一个私钥（密码）。只知道比特币地址是不能得知比特币私钥的，也是无法动用里面的比特币的，这和你知道别人的银行帐号但不知道别人的帐号密码不能取款和转账一样。但是和银行不一样的是，知道了比特币私钥却可以得知其比特币地址，并且能对该地址下的比特币进行转账，也就是说获得比特币私钥就拥有了该私钥和地址下的比特币的完全控制权——私钥就是一切！所以如果1：你弄丢了你的比特币私钥；或2：你的比特币私钥给别人知道了，你该地址下的比特币也就完蛋了：要么永远动用不了地址上的比特币，要么被知道你的私钥的人转走。

**私钥**
--------------------------------

**钱包最重要的就是私钥, 通过私钥可以算出比特比钱包里的一切**

私钥长度为256bit就是32字节,格式类似这样：5KJvsngHeMpm884wtkJNzQGaCErckhHJBGFsvd3VyK5qMZXj3hS

私钥可以导出为各种格式,bitaddress.org网站列了(WIF, WIFC, HEX, B64, B6, MINI, BIP38)7种格式,貌似官方的client只支持WIF，其实怎么导出都行，重要的是记住私钥，并且能在用的时候导入

- **WIF**: 51 characters base58, starts with a '5'  5Hu1CgigrdvBAn2FVf3yMYMqQ1kEndq5ZJ1sZY58FVaHem4dKmq, 默认方式，算法见https://en.bitcoin.it/wiki/Base58Check_encoding 和https://en.bitcoin.it/wiki/Wallet_import_format
- **WIFC** (WIF Compressed): 52 characters base58, starts with a 'K' or 'L' , KwaXtaJzv5wht9iWyUx7jfQxcaHqrRZD7Y6rAc9Czy7vVBzumFgE
- **HEX** (Hexadecimal Format): (64 characters [0-9A-F]): 0AB63E35696E930B3BC05A1CFD3AF8E9605714A8924B62DE4E0334F97422913F
- **B64** (Base64) (44 characters): CrY+NWlukws7wFoc/Tr46WBXFKiSS2LeTgM0+XQikT8=
- **B6**  (Base6) :好像是bitaddress自定义的，未实现
- **MINI**: 30 char https://en.bitcoin.it/wiki/Mini_private_key_format
- **BIP38**: 还是draft https://github.com/bitcoin/bips/blob/master/bip-0038.mediawiki

  https://en.bitcoin.it/wiki/Private_key

**公钥**:
--------------------------------

  只包含0-9,A-F字符,分为缩和非压缩格式, 压缩格式33字节,以0x2或者0x3开头，非压缩65字节,以0x4开头,以04 <x> <y>的形式给出，x和y是表示曲线上点的坐标的32字节字符串, 最新版本默认使用压缩格式
  Public Key (130 characters [0-9A-F]):
  Public Key (compressed, 66 characters [0-9A-F]):

**签名**:
--------------------------------

  用于交易,使用DER编码 http://en.wikipedia.org/wiki/Distinguished_Encoding_Rules#DER_encoding
  长度可能为73,72,71字节，各自生成的概率分别为 25%, 50% ,25%

**比特币地址**:
--------------------------------
  地址数目大于2^160(目前应该是2*62^33,最多应该是62^34-无效地址, 按https://en.bitcoin.it/wiki/Address算法，有效地址应该是4.29bill里有一个，大概应该是2^32有一个，所以最后有效数目大约是2^196)

  比特币地址开头为1

  长度为34个字符,但这个不一定，理论上可能会到27个，使用Public Key（压缩或者非压缩）生成,比如：

  15atBQgsbyhr7BQtUMjwdEXSHNgbthydR1 (非压缩Public Key：04BAEC0655BAF2851E271553AB0F4DDAB793FD0CCC587F41E03D07186DB54C521B7AEB90426C719ACE734BB4957C72777610D36F22DF5F660B1DD7CBBD7594B13B) 

  17FyT8NYV4Wf1DxrviCkpvVZ7i7bXx4rrx (压缩Public Key：03BAEC0655BAF2851E271553AB0F4DDAB793FD0CCC587F41E03D07186DB54C521B)

  https://en.bitcoin.it/wiki/List_of_address_prefixes

  https://en.bitcoin.it/wiki/Bitcoin_address


**Bitcoin地址计算过程**:
------------------------------------
  只包含0-9,A-F字符,分为缩和非压缩格式, 压缩格式33字节,以0x2或者0x3开头，非压缩65字节,以0x4开头,以04 <x> <y>的形式给出，x和y是表示曲线上点的坐标的32字节字符串, 最新版本默认使用压缩格式

  1. 产生ECDSA私钥，私钥是一个随机整数，公钥是私钥与基点的乘积,公钥产生后会有可靠性验证，防止攻击者利用性质较差的公钥进行攻击,避免偶然的编码或传输错误。使用性质较差的公钥会使其他安全措施无效(椭圆曲线数字签名算法（ECDSA）http://blog.sina.com.cn/s/blog_4e37c87d0100cuo0.html)如：

     18E14A7B6A307F426A94F8114701E7C8E774E7F9A47E2C2035DB29A206321725

  2. 计算出ECDSA公钥，

     0450863AD64A87AE8A2FE8….82BA6

  3. 对公钥进行SHA256运算

     600FFE422B4E00731A59557A5CCA46CC183944191006324A447BDB2D98D4B408

  4. 对第三步结果进行RIPEMD-160运算

     010966776006953D5567439E5E39F86A0D273BEE

  5. 在第四步结果上加上版本号

     00010966776006953D5567439E5E39F86A0D273BEE

  6. 对第五步结果进行SHA256运算

     445C7A8007A93D8733188288BB320A8FE2DEBD2AE1B47F0F50BC10BAE845C094

  7. 对第六步结果进行SHA256运算

     D61967F63C7DD183914A4AE452C9F6AD5D462CE3D277798075B107615C1A8A30

  8. 提出第七步结果的前四个字节

     D61967F6

  9. 将第八步的结果加到第五步结果最后面

     00010966776006953D5567439E5E39F86A0D273BEED61967F6

  10. 对第九步结果进行Base58编码

     16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM
     18stDaB2S9jKUi8R4qAke743NN2LyQi3mC
     1DbkKVxcwfuCCkM3cAQx8uTTA1K3CHEdu1
     15atBQgsbyhr7BQtUMjwdEXSHNgbthydR1
     17FyT8NYV4Wf1DxrviCkpvVZ7i7bXx4rrx
     15atBQgsbyhr7BQtUMjwdEXSHNgbthydR1
     5Hu1CgigrdvBAn2FVf3yMYMqQ1kEndq5ZJ1sZY58FVaHem4dKmq

* **相关算法**:
   - ECDSA http://en.wikipedia.org/wiki/Elliptic_Curve_DSA
   
          http://blog.sina.com.cn/s/blog_4e37c87d0100cuo0.html
   
          ECDSA 使用了 http://www.secg.org/collateral/sec2_final.pdf 中的secp256k1曲线
   
          https://en.bitcoin.it/wiki/ECDSA
   
   - RIPEMD-160  http://en.wikipedia.org/wiki/RIPEMD
   - SHA256 http://en.wikipedia.org/wiki/Sha-256
   - BASE58  https://en.bitcoin.it/wiki/Base58Check_encoding
 

参考:

  https://en.bitcoin.it/w/images/en/9/9b/PubKeyToAddr.png
  https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses

**多重签名地址**:
--------------------------------

  有些地址可以用2个private key生成，开头为3,用于一次交易付款到2个人的情况
 
钱包内容例子
--------------------------------
.. code-block:: bash 
   
   $./dbdump.py --datadir=/home/user/.litecoin/ --wallet
   PubKey 0202...4764 16wwC5qwPcVvLMR7m7A51Ed8BjTnKL9agE: PriKey 3081...4764
   PubKey 0205...9554 1B5wr4ABY2JJHaZU9bKSV4UxyHCLMjjmfT: PriKey 3081...9554
   PubKey 0206...bc57 13knSScn8HVo6S3GL6eDmRFYgdouWYyv5a: PriKey 3081...bc57
   PubKey 020b...bf68 12hnMR5hGByxQafwVM6icLt1pnnRqvZb2c: PriKey 3081...bf68
   PubKey 020d...b72b 15zktwJ7Z7R5tRfmUSyLdKRZTZNbdT1t3L: PriKey 3081...b72b
   PubKey 0210...3268 19egBQEFFf68oEE5Ndp3xZ8if7hd2UoncX: PriKey 3081...3268
   PubKey 0211...4c12 1N9oP8rBuosvDmPamfs3CvvzqTDzGgm6Bb: PriKey 3081...4c12
   PubKey 0213...f703 12Xz44Xnm76AK9cJueJw91uR7t5hUKb1UL: PriKey 3081...f703
   PubKey 0223...03c3 1HC7oFVgu12pmkpg7gZCPrBSwo4KR4SbjS: PriKey 3081...03c3
   PubKey 024e...5e69 1LdTNg6LkWKteEp3MiFZ7q2qvntSZ7UFmX: PriKey 3081...5e69
   PubKey 0250...5a1a 1CvuKADAYP1iLrQUN3wHpK6h32qTNynG3n: PriKey 3081...5a1a
   PubKey 0252...ce9e 1EiVQpzwtsuoSmnjoSEFKJJo4kprArrRmV: PriKey 3081...ce9e
   PubKey 0257...c2f0 1FkSrvrhHj7kw1zzp6t2beC7WyTZutTyiC: PriKey 3081...c2f0
   PubKey 025d...0522 1HeFKmUvQcMF2vE94d43Uf2YbxxBZKMX5Z: PriKey 3081...0522
   PubKey 025e...36c4 1MKtyt2YbqZZTUYFuz3uiqwP9o8H94ggPE: PriKey 3081...36c4
   PubKey 026c...9f77 1MyLUC9mcSSFjev4jxkmsvwddHjxJYCozu: PriKey 3081...9f77
   PubKey 026d...d77b 1GuuDXDTAnFRfRXp4YzkbpBJsNY2wNd63a: PriKey 3081...d77b
   PubKey 0271...c962 17prJPFZqzWte8zyhu8SSGT2A5YmD1Zahu: PriKey 3081...c962
   PubKey 0273...2bc3 1GLqMh7tNNo8tDb5BHHnDWoTnR5wfv161n: PriKey 3081...2bc3
   PubKey 0275...2128 18SsP2wkru6Jrk869tmtvjLyXRxxukMmEM: PriKey 3081...2128
   PubKey 027a...cf76 15oA7Bq1ZTdEczS3sL3EbEHbVph6n48Zwo: PriKey 3081...cf76
   PubKey 027e...2383 1P55xFGhBN4h6yyohvD2a1kzQgFTLnswEx: PriKey 3081...2383
   PubKey 027e...d714 1Kf7boaiQgdXffJFi58fyc9EwHjobhicEc: PriKey 3081...d714
   PubKey 028a...4f1d 1BZtNbMnQ9H8A3YTCwoxSTKyY51VdpfQuF: PriKey 3081...4f1d
   PubKey 0291...181d 1CXsBEiLUPJZNvU2tv4V1sm3FiTcaF5528: PriKey 3081...181d
   PubKey 0292...b90e 14LgbXDNAcyRdKGvkiVvgx1zsnXLREc9Wd: PriKey 3081...b90e
   PubKey 0294...a527 1CKCsv5Dhq43zddkbp8F9ea9nmhzBVwjwM: PriKey 3081...a527
   PubKey 0294...bc66 1MZdKntT7k7E2NefwBEjPU3P7qDdY3XZTa: PriKey 3081...bc66
   PubKey 0296...78b0 13ET1hXjY83TAz1wXhVDtsqaWTzH5kchmV: PriKey 3081...78b0
   PubKey 029f...bc0f 16mbXxBqFbzLymtXg2JEUp5ayhPpdKAitY: PriKey 3081...bc0f
   PubKey 02a0...16fd 1Q45J9NLbYpA6CEFL4d96ry1Wd3WUEPb2N: PriKey 3081...16fd
   PubKey 02a0...ce5e 135NWcYtQVuTHugUpRwv5KyjsuWFVbMnWy: PriKey 3081...ce5e
   PubKey 02a2...0545 17xDuY74tw2DEhTtmefuYUwkQTAcP5MCbf: PriKey 3081...0545
   PubKey 02a6...aef4 181rcaf18X8HH5hGsBnYU3WVYSGbqHj3qw: PriKey 3081...aef4
   PubKey 02ab...86a6 1LQyxF4Vh8hfkRAhXPieCTj6BxAYri3x1S: PriKey 3081...86a6
   PubKey 02ab...ad3b 1NnivfsvveBnHGZFwqXdxtimhxPrFAA5oo: PriKey 3081...ad3b
   PubKey 02b3...e37b 1C6f4pAzz6QKiNhDM39bahiWtqgt7fNunL: PriKey 3081...e37b
   PubKey 02b9...137e 1FQpTgkcjTMxAPxJTkFVbUdgENxMSUzY1G: PriKey 3081...137e
   PubKey 02bb...d05d 1HQP8tqBsqtBjVKmRGfUenBudaAWwjdqQQ: PriKey 3081...d05d
   PubKey 02bc...5f79 19KPAPZuvUsuzRNLsXohvrcB7S6daDkUNv: PriKey 3081...5f79
   PubKey 02c0...d759 13vFdbpAQ6vn2CaUVL6Nk7wUz91B12pPaj: PriKey 3081...d759
   PubKey 02c0...890e 1KfFfNn7YoN11LC7RfoySsaw9HZKTiEn7U: PriKey 3081...890e
   PubKey 02c8...7f33 19MEyuVYyFVT9HU2Jw5nFYJeunH8hqy3XB: PriKey 3081...7f33
   PubKey 02cd...f1a0 1NM1ikJ1x77iHDc8xChyXxQEWAFMvrVbW9: PriKey 3081...f1a0
   PubKey 02d1...cd9c 18WAsoGB9CzgJbVk26ueonBfJqoG3UnBTf: PriKey 3081...cd9c
   PubKey 02d3...5edc 1JyeoLtLdkuDXHjEUmczCuxFx2BGcqFLCH: PriKey 3081...5edc
   PubKey 02d8...e21a 1LixmuFJf1xRWoqCSM3co5qjAs7iNLtn18: PriKey 3081...e21a
   PubKey 02e0...9671 1LbKZfKEq23uxJNifQ6RCc6ouu1NFmy2Rx: PriKey 3081...9671
   PubKey 02e5...a054 1DrQUki1CMP3q4sVqKJvgVpCLmY9pWmBT7: PriKey 3081...a054
   PubKey 02e5...b988 1H2Brx4GKP5wLeptf1yNNtpGKYYymY8s6i: PriKey 3081...b988
   PubKey 02f0...f03e 164WNy3K3tKro4MZd5reeo5syuUE8ccyw5: PriKey 3081...f03e
   PubKey 02ff...478f 1EwpWzmJBkZHsVJsPLVPLWN3cVwoTioq4x: PriKey 3081...478f
   PubKey 0309...0897 1BA7oGAdaZTPUpsvqCcTd7Xm4PECGFDFGN: PriKey 3081...0897
   PubKey 0311...6266 1NZpmaUWW5tGej8fXowmkxNrbkADWQAUzf: PriKey 3081...6266
   PubKey 0314...89d9 1K3qX6hZDVwMAKkDZhAYs7V27iDkArYv1b: PriKey 3081...89d9
   PubKey 0315...eee0 1LuRKQGHxkmoBc5EirQ5tXMbqWa955qPSk: PriKey 3081...eee0
   PubKey 0318...7e18 1HL4iGtj5j4R5iWvWLoooMTcz41D663GZ5: PriKey 3081...7e18
   PubKey 0319...98c1 1PdmhTNLWraVLDu1tcMH341DoQNrxW9dTk: PriKey 3081...98c1
   PubKey 031b...e86f 1YyAUav5FYcuoE9CAMGSakQbTNpUFd3UL: PriKey 3081...e86f
   PubKey 0321...0ecd 1CGYYTBofm1dVoEJYrAJw3WeQXyxxzxjwe: PriKey 3081...0ecd
   PubKey 0321...dd85 1BRCZZJE94Mcsj7kPcCxWu7H3VMioZ5bf2: PriKey 3081...dd85
   PubKey 0326...2e71 12Nx5D7sk9eucJLqfp7SKbirjPUKmcCi4U: PriKey 3081...2e71
   PubKey 0326...51fa 18JhTQu2AuamB36RsqUbDVR9m9VkhvKF7d: PriKey 3081...51fa
   PubKey 0329...b6b9 1Jx7xdsdeVP4Aa9sXSYnMe4TyNhovsmtZA: PriKey 3081...b6b9
   PubKey 032f...e747 1B15a6uxwFurQUhon5kfTE2dbRngTjP2ij: PriKey 3081...e747
   PubKey 033e...7615 17hkF8SzQ9c3PteAq58nx7xrCzBc9HM4Yr: PriKey 3081...7615
   PubKey 0342...92c2 18NAYE2icG1h7NdMphfWe5dzVA5357kwFb: PriKey 3081...92c2
   PubKey 0345...e503 15fzBtEZig4SDSKsV9vqrjM1KgbAkubgZM: PriKey 3081...e503
   PubKey 0349...3c44 13pPAZ5PAAtjxk2EAAGHEZhA9b7kh5jKpY: PriKey 3081...3c44
   PubKey 0356...7eb8 1PBnVe6Tygf5tstjjNCbKfEWeonie9DQSR: PriKey 3081...7eb8
   PubKey 0357...90ef 1GKXRQRwqybKnZFb2ckFBJxAGDHB6XCTKq: PriKey 3081...90ef
   PubKey 0359...06b4 1FzZQQXve55MgYiZ3LRtnWBe46TQfeYv5X: PriKey 3081...06b4
   PubKey 0362...f91d 1CBqtpd3Vd26bf7p6EvN8jchKJNVgMDsQ3: PriKey 3081...f91d
   PubKey 0370...e3b5 1KxTTSfTJipwRoM38rcvX2noJ7CWP9Qikf: PriKey 3081...e3b5
   PubKey 0379...ecee 1Beyv4jDRtJ8mjjrKwRfxxe8xhbodQv6Q2: PriKey 3081...ecee
   PubKey 0382...518c 1JbQRj3ke3HcLgqJy5pEM2URBZkzt8ST4W: PriKey 3081...518c
   PubKey 0382...5eab 1L3Nrni7HBuGfgpq2etks6r8wDEUybCN3J: PriKey 3081...5eab
   PubKey 0382...ddd4 17PAiH66pzYyWwzL46bxEK7tDkc1AmWdj9: PriKey 3081...ddd4
   PubKey 0389...e733 18LD9WB2YwdyyHcHn4WMxZu8QKjfHtc9eG: PriKey 3081...e733
   PubKey 0391...84d8 17CLqXHGP3h4HRTtbUWmtX95hgK5GzwVAU: PriKey 3081...84d8
   PubKey 039b...2b55 1NvLESPpzzwCiLozbSCh46UdFpYH31rjvX: PriKey 3081...2b55
   PubKey 03a1...6663 16udfMYPtcih6WXN7Fqewk5AkumzsuVs8G: PriKey 3081...6663
   PubKey 03a4...e212 1AJN9aB6wJFQoo4gHmQMTZ3wHCvzZ7rnmc: PriKey 3081...e212
   PubKey 03aa...9b55 1sahDj1anDb5HDhRoAU6QGYSv3zxrDFWK: PriKey 3081...9b55
   PubKey 03b6...86c4 1PjG8U12LpTr8LjCx5NU64DXnGkwBvuKwB: PriKey 3081...86c4
   PubKey 03bd...7c7b 18KyQEejPsqm2FPsnpoNnF5J8qRvhhNfzo: PriKey 3081...7c7b
   PubKey 03c1...2527 1GpgeBiCEEea48L1nQptVnZ8QkgH8rKtdj: PriKey 3081...2527
   PubKey 03ca...0589 1PgAsupmDWmZivRSRjVSbaZoBVhJfic7Kh: PriKey 3081...0589
   PubKey 03cf...34a9 13z33FqvsqPU67KS9YxxcrFLJ3ZafDWZwy: PriKey 3081...34a9
   PubKey 03d1...a31e 1FrJmb6CTLWUYbz745Hf7QAR2QgQtu33dL: PriKey 3081...a31e
   PubKey 03d2...05cd 1Ly8V3o1g4pSN5FSut86zT9sNPqvd75k33: PriKey 3081...05cd
   PubKey 03d6...5f9c 1BqedRMeRBkBRAR7Y1B9N9jz1Aw3Unn5Hk: PriKey 3081...5f9c
   PubKey 03d6...4aae 18jfZMmnCkZUh1gUKqA7UQguio6Nc6dvtA: PriKey 3081...4aae
   PubKey 03d9...93ee 1KyysazxSmCNLR9h8Xd6QNakaw1ywhoLY1: PriKey 3081...93ee
   PubKey 03da...f4a0 1FBWXExYjxt9hBf9zhoWRZ4WwtpKwAQFP8: PriKey 3081...f4a0
   PubKey 03dc...c2bb 1Nymut2K67nwQF1zdmTwEtRQg8QktU6t5P: PriKey 3081...c2bb
   PubKey 03e0...dc82 128ijWi5Yun1Xq9KMEBYh2wkbgapNECW7b: PriKey 3081...dc82
   PubKey 03e5...21d0 1LCzAJbxSWXjArk8VMzjSdLGdCUQNCsTxS: PriKey 3081...21d0
   PubKey 03e8...bb10 1DXaKhxtymfCuJokAdeSwAjfwrpwEzaM4c: PriKey 3081...bb10
   PubKey 03ea...b45a 133Nk5PoW3tbfLaXh63TMbYNcnSa1ngdeJ: PriKey 3081...b45a
   PubKey 03fb...a543 1BNdJiBNK2jyyrqD7FMANTb8E9Sd1tZkcm: PriKey 3081...a543
   ADDRESS LcYv171SfQtqtjyJMZ2oVfQdRoSzU8NxuV : fao
   ADDRESS LhxDPgJrRUhuP9RN8DMmN5HHzV8DK2u2AC : 
   Change Pool key 2: 1LbKZfKEq23uxJNifQ6RCc6ouu1NFmy2Rx (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 3: 1EwpWzmJBkZHsVJsPLVPLWN3cVwoTioq4x (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 4: 1LCzAJbxSWXjArk8VMzjSdLGdCUQNCsTxS (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 5: 1CXsBEiLUPJZNvU2tv4V1sm3FiTcaF5528 (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 6: 1DrQUki1CMP3q4sVqKJvgVpCLmY9pWmBT7 (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 7: 1PBnVe6Tygf5tstjjNCbKfEWeonie9DQSR (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 8: 1HC7oFVgu12pmkpg7gZCPrBSwo4KR4SbjS (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 9: 1HeFKmUvQcMF2vE94d43Uf2YbxxBZKMX5Z (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 10: 1Beyv4jDRtJ8mjjrKwRfxxe8xhbodQv6Q2 (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 11: 17PAiH66pzYyWwzL46bxEK7tDkc1AmWdj9 (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 12: 18KyQEejPsqm2FPsnpoNnF5J8qRvhhNfzo (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 13: 1PgAsupmDWmZivRSRjVSbaZoBVhJfic7Kh (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 14: 1NvLESPpzzwCiLozbSCh46UdFpYH31rjvX (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 15: 1MKtyt2YbqZZTUYFuz3uiqwP9o8H94ggPE (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 16: 12Xz44Xnm76AK9cJueJw91uR7t5hUKb1UL (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 17: 1NM1ikJ1x77iHDc8xChyXxQEWAFMvrVbW9 (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 18: 1YyAUav5FYcuoE9CAMGSakQbTNpUFd3UL (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 19: 1BNdJiBNK2jyyrqD7FMANTb8E9Sd1tZkcm (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 20: 18LD9WB2YwdyyHcHn4WMxZu8QKjfHtc9eG (Time: Tue Apr  2 14:16:50 2013)
   Change Pool key 21: 1FzZQQXve55MgYiZ3LRtnWBe46TQfeYv5X (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 22: 13vFdbpAQ6vn2CaUVL6Nk7wUz91B12pPaj (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 23: 18jfZMmnCkZUh1gUKqA7UQguio6Nc6dvtA (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 24: 1BA7oGAdaZTPUpsvqCcTd7Xm4PECGFDFGN (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 25: 1MyLUC9mcSSFjev4jxkmsvwddHjxJYCozu (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 26: 1GpgeBiCEEea48L1nQptVnZ8QkgH8rKtdj (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 27: 1BqedRMeRBkBRAR7Y1B9N9jz1Aw3Unn5Hk (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 28: 1C6f4pAzz6QKiNhDM39bahiWtqgt7fNunL (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 29: 13z33FqvsqPU67KS9YxxcrFLJ3ZafDWZwy (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 30: 164WNy3K3tKro4MZd5reeo5syuUE8ccyw5 (Time: Tue Apr  2 14:16:51 2013)
   Change Pool key 31: 17CLqXHGP3h4HRTtbUWmtX95hgK5GzwVAU (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 32: 18JhTQu2AuamB36RsqUbDVR9m9VkhvKF7d (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 33: 17prJPFZqzWte8zyhu8SSGT2A5YmD1Zahu (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 34: 1KfFfNn7YoN11LC7RfoySsaw9HZKTiEn7U (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 35: 1GLqMh7tNNo8tDb5BHHnDWoTnR5wfv161n (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 36: 15fzBtEZig4SDSKsV9vqrjM1KgbAkubgZM (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 37: 1FrJmb6CTLWUYbz745Hf7QAR2QgQtu33dL (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 38: 1LixmuFJf1xRWoqCSM3co5qjAs7iNLtn18 (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 39: 1B15a6uxwFurQUhon5kfTE2dbRngTjP2ij (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 40: 1CGYYTBofm1dVoEJYrAJw3WeQXyxxzxjwe (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 41: 1GKXRQRwqybKnZFb2ckFBJxAGDHB6XCTKq (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 42: 12Nx5D7sk9eucJLqfp7SKbirjPUKmcCi4U (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 43: 19egBQEFFf68oEE5Ndp3xZ8if7hd2UoncX (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 44: 1L3Nrni7HBuGfgpq2etks6r8wDEUybCN3J (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 45: 1NZpmaUWW5tGej8fXowmkxNrbkADWQAUzf (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 46: 1Kf7boaiQgdXffJFi58fyc9EwHjobhicEc (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 47: 16wwC5qwPcVvLMR7m7A51Ed8BjTnKL9agE (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 48: 1LQyxF4Vh8hfkRAhXPieCTj6BxAYri3x1S (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 49: 1FQpTgkcjTMxAPxJTkFVbUdgENxMSUzY1G (Time: Tue Apr  2 14:16:52 2013)
   Change Pool key 50: 1N9oP8rBuosvDmPamfs3CvvzqTDzGgm6Bb (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 51: 1CBqtpd3Vd26bf7p6EvN8jchKJNVgMDsQ3 (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 52: 1Ly8V3o1g4pSN5FSut86zT9sNPqvd75k33 (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 53: 1CKCsv5Dhq43zddkbp8F9ea9nmhzBVwjwM (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 54: 1NnivfsvveBnHGZFwqXdxtimhxPrFAA5oo (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 55: 1FkSrvrhHj7kw1zzp6t2beC7WyTZutTyiC (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 56: 1EiVQpzwtsuoSmnjoSEFKJJo4kprArrRmV (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 57: 1K3qX6hZDVwMAKkDZhAYs7V27iDkArYv1b (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 58: 13ET1hXjY83TAz1wXhVDtsqaWTzH5kchmV (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 59: 15zktwJ7Z7R5tRfmUSyLdKRZTZNbdT1t3L (Time: Tue Apr  2 14:16:53 2013)
   Change Pool key 60: 1JbQRj3ke3HcLgqJy5pEM2URBZkzt8ST4W (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 61: 15oA7Bq1ZTdEczS3sL3EbEHbVph6n48Zwo (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 62: 1Q45J9NLbYpA6CEFL4d96ry1Wd3WUEPb2N (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 63: 13pPAZ5PAAtjxk2EAAGHEZhA9b7kh5jKpY (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 64: 16mbXxBqFbzLymtXg2JEUp5ayhPpdKAitY (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 65: 19KPAPZuvUsuzRNLsXohvrcB7S6daDkUNv (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 66: 1Nymut2K67nwQF1zdmTwEtRQg8QktU6t5P (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 67: 18SsP2wkru6Jrk869tmtvjLyXRxxukMmEM (Time: Tue Apr  2 14:16:54 2013)
   Change Pool key 68: 1LdTNg6LkWKteEp3MiFZ7q2qvntSZ7UFmX (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 69: 17xDuY74tw2DEhTtmefuYUwkQTAcP5MCbf (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 70: 1B5wr4ABY2JJHaZU9bKSV4UxyHCLMjjmfT (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 71: 1FBWXExYjxt9hBf9zhoWRZ4WwtpKwAQFP8 (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 72: 1BRCZZJE94Mcsj7kPcCxWu7H3VMioZ5bf2 (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 73: 13knSScn8HVo6S3GL6eDmRFYgdouWYyv5a (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 74: 1AJN9aB6wJFQoo4gHmQMTZ3wHCvzZ7rnmc (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 75: 18WAsoGB9CzgJbVk26ueonBfJqoG3UnBTf (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 76: 1BZtNbMnQ9H8A3YTCwoxSTKyY51VdpfQuF (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 77: 16udfMYPtcih6WXN7Fqewk5AkumzsuVs8G (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 78: 1HL4iGtj5j4R5iWvWLoooMTcz41D663GZ5 (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 79: 1LuRKQGHxkmoBc5EirQ5tXMbqWa955qPSk (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 80: 12hnMR5hGByxQafwVM6icLt1pnnRqvZb2c (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 81: 1Jx7xdsdeVP4Aa9sXSYnMe4TyNhovsmtZA (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 82: 14LgbXDNAcyRdKGvkiVvgx1zsnXLREc9Wd (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 83: 1MZdKntT7k7E2NefwBEjPU3P7qDdY3XZTa (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 84: 128ijWi5Yun1Xq9KMEBYh2wkbgapNECW7b (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 85: 1GuuDXDTAnFRfRXp4YzkbpBJsNY2wNd63a (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 86: 133Nk5PoW3tbfLaXh63TMbYNcnSa1ngdeJ (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 87: 1P55xFGhBN4h6yyohvD2a1kzQgFTLnswEx (Time: Tue Apr  2 14:16:55 2013)
   Change Pool key 88: 1DXaKhxtymfCuJokAdeSwAjfwrpwEzaM4c (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 89: 1H2Brx4GKP5wLeptf1yNNtpGKYYymY8s6i (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 90: 18NAYE2icG1h7NdMphfWe5dzVA5357kwFb (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 91: 1KyysazxSmCNLR9h8Xd6QNakaw1ywhoLY1 (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 92: 135NWcYtQVuTHugUpRwv5KyjsuWFVbMnWy (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 93: 181rcaf18X8HH5hGsBnYU3WVYSGbqHj3qw (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 94: 1PdmhTNLWraVLDu1tcMH341DoQNrxW9dTk (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 95: 19MEyuVYyFVT9HU2Jw5nFYJeunH8hqy3XB (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 96: 1sahDj1anDb5HDhRoAU6QGYSv3zxrDFWK (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 97: 17hkF8SzQ9c3PteAq58nx7xrCzBc9HM4Yr (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 98: 1HQP8tqBsqtBjVKmRGfUenBudaAWwjdqQQ (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 99: 1CvuKADAYP1iLrQUN3wHpK6h32qTNynG3n (Time: Tue Apr  2 14:16:56 2013)
   Change Pool key 100: 1JyeoLtLdkuDXHjEUmczCuxFx2BGcqFLCH (Time: Tue Apr  2 14:16:57 2013)
   Change Pool key 101: 1KxTTSfTJipwRoM38rcvX2noJ7CWP9Qikf (Time: Tue Apr  2 14:16:57 2013)
   addrIncoming: 
   Version: 60300
   Default Key: 0x03b6...86c4 1PjG8U12LpTr8LjCx5NU64DXnGkwBvuKwB
   Skipping item of type minversion

https://en.bitcoin.it/wiki/Accounts_explained

钱包相关
-------------------
钱包加密

https://en.bitcoin.it/wiki/Wallet_encryption

private key 加密
Wallet encryption uses AES-256-CBC to encrypt only the private keys.

https://en.bitcoin.it/wiki/Wallet_encryption

钱包解密服务

http://www.walletrecoveryservices.com/

一些脚本

http://pastebin.com/XWibUePh

http://pastebin.com/9MYBV9ve

https://bitcointalk.org/index.php?topic=85495.0;all

国人的工具，收费

http://xingfeng.org/?p=5


* 找零机制：

为了保证隐私（匿名性）, 许多客户端默认设置（Armory, Electrum）每转出一笔钱就把剩下的自动转移到钱包里面新的一个地址名下

* 导入导出

https://bitcointalk.org/index.php?topic=4448.0

* 备份

http://yibite.com/portal.php?mod=topic&topic=brainwallet

* 纸钱包:

https://en.bitcoin.it/wiki/Paper_wallet

* 硬件钱包

  http://www.pi-wallet.com/

* 在线钱包

 https://blockchain.info/wallet/wallet-format

生成钱包:

https://www.bitaddress.org/

钱包的安全
-------------------

李笑来的建议:
1. 不要在交易平台留存大量的比特币。

2. 使用比特币在线服务的时候，一定要设置二次验证。

3. http开头（而不是https开头）的网站是也过分业余的，别用。

4. 最好远离Windows操作平台。

5. 为自己的币做几个纸钱包

6. 脑钱包并不完美 — 万一摔个跟头，脑震荡，然后恰好那部分失忆了怎么版？

7. 比特币其实并不是匿名的，所有的交易记录都是公开的。所以，别以为你干什么别人不知道。

8. 花钱买个正版的1Password很值

9. 绝对不能在多个网站使用同样的用户名和同样的密码

10. 不能把钱包文件保存在云端（dropbox等等）——哪怕是压缩加密过的

离线钱包:

https://en.bitcoin.it/wiki/Securing_your_wallet

Armory的冷储存和离线交易功能:

http://www.8btc.com/wiki/bitcoin-wallet-safe-use-perfect-backup


地址有效性检查：
-------------------

这儿有些python,php,.net,rudy的例子:

https://bitcointalk.org/index.php?topic=1026.0

http://rosettacode.org/wiki/Bitcoin/address_validation

.. code-block:: python

    from hashlib import sha256
     
    digits58 = '123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz'
     
    def decode_base58(bc, length):
        n = 0
        for char in bc:
            n = n * 58 + digits58.index(char)
        return n.to_bytes(length, 'big')
     
    def check_bc(bc):
        bcbytes = decode_base58(bc, 25)
        return bcbytes[-4:] == sha256(sha256(bcbytes[:-4]).digest()).digest()[:4]
     
    if __name__ == '__main__':
        bc = '1AGNa15ZQXAZUgFiqJ2i7Z2DPU2J6hW62i'
        assert check_bc(bc)
        assert not check_bc( bc.replace('N', 'P', 1) )
        assert check_bc('1111111111111111111114oLvT2')
        assert check_bc("17NdbrSGoUotzeGCcMMCqnFkEvLymoou9j")

send coin from commmand line:
------------------------------

bitcoind -rpcpassword=xxxxxxxxxx sendtoaddress address 0.001

余额计算
-------------------

计算方式1:
 Cwallet::getbalance() 遍历 wallet 里的交易，累计每一笔没有标记为spent的out

计算方式2:
getbalance() 遍历所有wallet里的交易，分为sent 和received两个列表，分别汇总，然后sum(received) - sum(send) - sum(fee),结果应该和第一种相同

余额 =  sum(TxOutToMe) - Sum(TxOutToOther) - fee

小技巧
-------------------
If you need to give someone, say, 5 BTC as a birthday gift, you:

- Generate public/private key pair
- Transfer 5 BTC to that pair
- Export (possibly with delete) the key from wallet
- Convert exported key to a QR code
- print the code on a postcard

QA
-------------------
Q. 有效的钱包地址有多少个，如何计算?

A. 2^160

Q. 钱包地址相同的概率,相同之后?

A. 地址数目非常大，相同概率很低，但如果你生成某人的地址相同,那么你就得到了对方的私钥，得到对方所有比特币

Q. 地址使用压缩或者非压缩Public Key 生成，是否意味着，一个secret key可以生成2个有效地址?

A. 正确, 一个私钥可以对应两个有效比特币地址

Q. wallet 有那些限制， 存放多少交易？ 

A. keypool=100
   only one account
   只能支持1个帐号，解决办法生成更多地址

   https://bitcointalk.org/index.php?topic=5324.20
 

