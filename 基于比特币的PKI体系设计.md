## 基于比特币的PKI体系设计

### 引言

方案号：BPKI-01

使用比特币作为承载与信任根构建PKI体系。

承载是指，PKI体系的各类数据（如CA证书、中间证书、叶子证书、吊销记录等）均在链上，所有PKI相关动作（如证书签发、吊销）均在链上有可以验证的记录。

信任根是指，CA证书或对CA证书进行签名使用的密码算法及密钥对为比特币对应的密码算法及密钥对，即，对CA证书的签名使用ECDSA，且密钥可在比特币中找到对应地址。

其余部分与通常的PKI体系相同，并采用X509证书格式。

#### 典型X509证书格式与比特币密钥对对应关系

##### 证书格式

~~~ASN.1
Certificate::=SEQUENCE{
         tbsCertificate      TBSCertificate,
          signatureAlgorithm  AlgorithmIdentifier,
          signatureValue      BIT STRING
 
}
~~~

~~~ASN.1
TBSCertificate::=SEQUENCE{
    version           [0]   EXPLICIT Version DEFAULT v1,
 
    serialNumber            CertificateSerialNumber,
 
    signature               AlgorithmIdentifier,
 
    issuer                  Name,
 
    validity                Validity,
 
    subject                 Name,
 
    subjectPublicKeyInfo    SubjectPublicKeyInfo,
 
    issuerUniqueID    [1]   IMPLICIT UniqueIdentifier OPTIONAL,
 
    subjectUniqueID   [2]   IMPLICIT UniqueIdentifier OPTIONAL,
    extensions        [3]   EXPLICIT Extensions OPTIONAL
}
~~~

##### 自签名CA证书样本（Base64）

~~~
-----BEGIN CERTIFICATE-----
MIICGDCCAb2gAwIBAgIJAL9fOvxY+NlSMAoGCCqGSM49BAMCMGgxCzAJBgNVBAYT
AkNOMQ4wDAYDVQQIDAVDaGluYTEQMA4GA1UEBwwHQmVpamluZzETMBEGA1UECgwK
TW9ua2V5bG9yZDENMAsGA1UECwwEVGVzdDETMBEGA1UEAwwKTW9ua2V5bG9yZDAe
Fw0xOTA4MDgwNTI2MDJaFw0yOTA4MDUwNTI2MDJaMGgxCzAJBgNVBAYTAkNOMQ4w
DAYDVQQIDAVDaGluYTEQMA4GA1UEBwwHQmVpamluZzETMBEGA1UECgwKTW9ua2V5
bG9yZDENMAsGA1UECwwEVGVzdDETMBEGA1UEAwwKTW9ua2V5bG9yZDBWMBAGByqG
SM49AgEGBSuBBAAKA0IABM9PjbJOtclq+ZztFqxuUkXvBdppmmkj6a/zgn/tpbsg
JB/uE83b/+XzI5RpW07BYXDAKoqpHrg9Nff+xdJ53LmjUzBRMB0GA1UdDgQWBBSb
/11AaEiyI9VoRo+k57qZmaBrxDAfBgNVHSMEGDAWgBSb/11AaEiyI9VoRo+k57qZ
maBrxDAPBgNVHRMBAf8EBTADAQH/MAoGCCqGSM49BAMCA0kAMEYCIQC5rPWCYMkp
ptwvelwmI6Z14FHgJX4m23NpFnh6tTRo6QIhAO+lPTQudLvb1c1twqKRX9OeyTKO
6RHOJPS+z7QunL7M
-----END CERTIFICATE-----
~~~

##### ASN1格式

~~~ASN.1
SEQUENCE (3 elem)
  SEQUENCE (8 elem)
    [0] (1 elem)
      INTEGER 2
    INTEGER (64 bit) 13789805439530621266
    SEQUENCE (1 elem)
      OBJECT IDENTIFIER 1.2.840.10045.4.3.2 ecdsaWithSHA256 (ANSI X9.62 ECDSA algorithm with SHA256)
    SEQUENCE (6 elem)
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.6 countryName (X.520 DN component)
          PrintableString CN
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.8 stateOrProvinceName (X.520 DN component)
          UTF8String China
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.7 localityName (X.520 DN component)
          UTF8String Beijing
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.10 organizationName (X.520 DN component)
          UTF8String Monkeylord
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.11 organizationalUnitName (X.520 DN component)
          UTF8String Test
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.3 commonName (X.520 DN component)
          UTF8String Monkeylord
    SEQUENCE (2 elem)
      UTCTime 2019-08-08 05:26:02 UTC
      UTCTime 2029-08-05 05:26:02 UTC
    SEQUENCE (6 elem)
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.6 countryName (X.520 DN component)
          PrintableString CN
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.8 stateOrProvinceName (X.520 DN component)
          UTF8String China
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.7 localityName (X.520 DN component)
          UTF8String Beijing
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.10 organizationName (X.520 DN component)
          UTF8String Monkeylord
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.11 organizationalUnitName (X.520 DN component)
          UTF8String Test
      SET (1 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.4.3 commonName (X.520 DN component)
          UTF8String Monkeylord
    SEQUENCE (2 elem)
      SEQUENCE (2 elem)
        OBJECT IDENTIFIER 1.2.840.10045.2.1 ecPublicKey (ANSI X9.62 public key type)
        OBJECT IDENTIFIER 1.3.132.0.10 secp256k1 (SECG (Certicom) named elliptic curve)
      BIT STRING (520 bit) 0000010011001111010011111000110110110010010011101011010111001001011010…
    [3] (1 elem)
      SEQUENCE (3 elem)
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.29.14 subjectKeyIdentifier (X.509 extension)
          OCTET STRING (1 elem)
            OCTET STRING (20 byte) 9BFF5D406848B223D568468FA4E7BA9999A06BC4
        SEQUENCE (2 elem)
          OBJECT IDENTIFIER 2.5.29.35 authorityKeyIdentifier (X.509 extension)
          OCTET STRING (1 elem)
            SEQUENCE (1 elem)
              [0] (20 byte) 9BFF5D406848B223D568468FA4E7BA9999A06BC4
        SEQUENCE (3 elem)
          OBJECT IDENTIFIER 2.5.29.19 basicConstraints (X.509 extension)
          BOOLEAN true
          OCTET STRING (1 elem)
            SEQUENCE (1 elem)
              BOOLEAN true
  SEQUENCE (1 elem)
    OBJECT IDENTIFIER 1.2.840.10045.4.3.2 ecdsaWithSHA256 (ANSI X9.62 ECDSA algorithm with SHA256)
  BIT STRING (1 elem)
    SEQUENCE (2 elem)
      INTEGER (256 bit) 8398346912588692790750890663641359071361224854764571405243949789847804…
      INTEGER (256 bit) 1083947229904258680780879442286504260723640719256188292384974456633883…
~~~

其中以下字段即比特币使用的签名值：

~~~ASN.1
    SEQUENCE (2 elem)
      INTEGER (256 bit) 8398346912588692790750890663641359071361224854764571405243949789847804…
      INTEGER (256 bit) 1083947229904258680780879442286504260723640719256188292384974456633883…
~~~

该值对TBSCertificate的DER编码结果进行签名，签名来自上级证书。（此处为自签名，即自己）

其中以下字段即比特币使用的公钥：

~~~ASN.1
      BIT STRING (520 bit) 0000010011001111010011111000110110110010010011101011010111001001011010…
~~~

该/公钥为该证书的公钥。

### 需求定义

本部分定义了基于比特币的PKI体系所应实现的基础功能需求，包含了PKI需求部分和链上需求部分。

#### PKI需求部分

应实现以下功能：

>根证书产生：产生对应比特币密钥对（ECDSA secp256k1）的CA证书
>
>子证书签发：通过CA签发任意密码算法的子证书，若子证书用途为中间证书（即继续签发子证书）或对比特币地址进行认证的证书，那么也应当采用对应比特币密钥对（ECDSA secp256k1）。
>
>子证书吊销：通过CA吊销已签发的证书。

#### 链上需求部分

应实现以下功能：

>根证书声明和分发：通过metanet协议实现根证书的声明，并可通过对特定区块链数据的查询获取CA证书。
>
>子证书签发声明（和分发）：通过metanet协议签发子证书，并且用户可通过对特定区块链数据的查询获取子证书的哈希值（或内容），并且用户可以验证其链上记录来自CA。
>
>子证书吊销声明（和吊销记录分发）：通过metanet协议公布对于子证书的吊销记录，并且用户可通过对特定区块链数据的查询获取吊销记录，并且用户可以验证其链上记录来自CA。
>
>证书验证：用户可以通过特定方法，对特定区块链数据进行查询，确认证书的哈希值或证书内容在链上，并且记录来自CA。

### 设计原理

整体设计分为PKI体系和链上协议两部分，两部分基于同样的ECDSA secp256k1密钥对。

PKI体系操作和链上协议操作互为镜像，即，每进行一次PKI动作应同时完成对应链上动作，通过链上动作结果可还原（或验证）每个PKI动作结果。

为简化设计，可约定，每个动作为原子操作，包含先后两个子动作：PKI动作、链上镜像动作。

两个子动作均完成时，认为动作完成，否则应进行回滚，放弃PKI动作结果。

####  PKI体系

应搭建传统的PKI服务器。

应采用封装的PKI工具，封装的PKI工具以传统PKI工具为基础或封装传统PKI工具，但当且仅当链上镜像动作完成后才返回成功结果。

证书及吊销记录遵从X509格式。

#### 链上协议

应包含CA证书声明协议，协议构造一个链上记录（TX），该记录包含一个可作为数据解析的输出，一个可被花费的输出，若该记录为非coinbase类型，则同时应包含一个经过对CA证书进行签名的公钥（通常为CA公钥）签名的输入。该记录的可作为数据解析的输出中应包含CA证书全部内容数据。该记录的第一个可被花费的输出的花费与否被认为是一个标志位，标志当前CA证书是否有效。

应包含子证书签发协议，协议构造一个链上记录（TX），该记录包含一个包含CA公钥签名的输入，一个可作为数据解析的输出，一个可被花费的输出。该记录的可作为数据解析的输出中应包含被签发子证书全部内容数据（或对应哈希值）。该记录的第一个可被花费的输出的花费与否被认为是一个标志位，标志当前被签发证书是否有效。

应包含证书吊销协议，协议构造一个链上记录，该记录包含一个花费了被吊销证书第一个可被花费输出的输入，若包含可被作为数据解析的输出，则该输出中应包含被吊销证书的吊销记录。

以上三个协议中若存在可以作为数据解析的输出，则该输出中应包含索引字段，使用户和相关服务提供商能够识别和查询相应的链上记录（TX）。索引字段可以为bitcom协议标识符，也可以为一个能够从其包含证书公钥确定性地构造得到的输出。

### 功能设计

#### PKI体系部分设计

PKI工具采用openssl

##### CA密钥对生成

~~~bash
openssl ecparam -genkey -name secp256k1 -out root_ca.key
~~~

##### 创建CA证书

~~~bash
openssl req -new -sha256 -x509 -days 3650 -extensions v3_ca -key root_ca.key -out root_ca.crt
~~~

##### 签发子证书 - 证书签发请求（CSR）

注意：该过程通常由用户进行，若用户私钥为CA生成则也可由CA代理。

~~~bash
openssl req -new -sha256 -key subcert.key -out subcert.csr -extensions v3_req
~~~

注意：`subcert.key`可来自链上经过keyUtil转换而来，或由openssl生成。

若由openssl生成

~~~bash
openssl ecparam -genkey -name secp256k1 -out subcert.key
~~~

若由keyUtil转换

~~~bash
node cli.js key2pem -key [私钥] -out subcert.key
~~~

##### 签发子证书 - 签名

注意：还有CA证书目录、吊销目录、证书序号等CA配置需要提前完成。

~~~bash
openssl ca -days 3650 -cert root_ca.crt -keyfile root_ca.key -md sha256 -extensions v3_req -in subcert.csr -out subcert.crt
~~~

##### 子证书吊销

参照openssl PKI中的证书吊销

#### 链上协议设计

##### CA证书声明协议

链上记录格式如下

~~~
输入1: 包含公钥签名的输入（若为coinbase则可省略）
	PK为签名CA证书的公钥

输出1: OP_RETURN输出
	OP_RETURN <Metanet Flag> <CA公钥𝑃ca> <null(CA是孤点)> <CA证书subject信息（用于索引）> <CA证书crt文件内容>
输出2：可花费的输出（一般为P2PKH，但不限于此）
	输出对应的公钥可来自密钥衍生，也可来自其他途径
	花费该输出意味着证书被吊销
其他输出及找零
~~~

用户可通过BITCOM协议标识来搜索所有链上的CA证书。

用户可以通过搜索CA证书内容，来搜索到链上CA证书声明记录。

用户也可通过签名CA证书的公钥确定性地衍生出该记录的输出2，从而搜索链上记录（TX）和UTXO。

##### 子证书签发协议

链上记录格式如下

```
输入1: P2PKH输入
	PK为CA证书公钥

输出1: OP_RETURN输出
	OP_RETURN <Metanet Flag> <子证书公钥𝑃cert> <𝑇𝑥𝐼𝐷ca> <子证书subject信息（用于索引）> <证书crt文件内容>
输出2：可花费的输出（一般为P2PKH，但不限于此）
	输出对应的公钥可来自密钥衍生，也可来自其他途径
	花费该输出意味着证书被吊销
其他输出及找零
```

用户可通过BITCOM协议标识及CA证书公钥来搜索该CA的所有链上的证书签发记录（不推荐）。

用户可以通过搜索被签发证书内容（或哈希值），来搜索到链上证书签发记录。

用户也可通过被签发证书公钥或CA公钥确定性地衍生出该记录的输出2，从而搜索链上记录（TX）和UTXO。

##### 证书吊销协议

链上记录格式如下

```
输入1: P2PKH输入
	花费被吊销证书的输出2

输出1: OP_RETURN输出（Optional）
	OP_RETURN <Metanet Flag> <花费输入1的公钥𝑃revoke> <𝑇𝑥𝐼𝐷cert> <被吊销证书的吊销记录>
输出2：其他输出及找零
```

