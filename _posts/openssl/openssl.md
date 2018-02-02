创建自己的CA机构

为CA生成私钥
 openssl genrsa -out ca-key.pem -des 1024

通过CA私钥生成CSR
 openssl req -new -key ca-key.pem -out ca-csr.pem

通过CSR文件和私钥生成CA证书
 openssl x509 -req -in ca-csr.pem -signkey ca-key.pem -out ca-cert.pem

 可能会遇到的问题

 你需要root或者admin的权限
Unable to load config info from /user/local/ssl/openssl.cnf
对于这个问题，你可以从网上下载一份正确的openssl.cnf文件，
然后set OPENSSL_CONF=openssl.cnf文件的本地路径

创建服务器端证书

为服务器生成私钥
 openssl genrsa -out server-key.pem 1024

利用服务器私钥文件服务器生成CSR
 openssl req -new -key server-key.pem -config openssl.cnf -out server-csr.pem

这一步非常关键，你需要指定一份openssl.cnf文件。可以用这个

[req]  
    distinguished_name = req_distinguished_name  
    req_extensions = v3_req  

    [req_distinguished_name]  
    countryName = Country Name (2 letter code)  
    countryName_default = CN  
    stateOrProvinceName = State or Province Name (full name)  
    stateOrProvinceName_default = BeiJing  
    localityName = Locality Name (eg, city)  
    localityName_default = YaYunCun  
    organizationalUnitName  = Organizational Unit Name (eg, section)  
    organizationalUnitName_default  = Domain Control Validated  
    commonName = Internet Widgits Ltd  
    commonName_max  = 64  

    [ v3_req ]  
    # Extensions to add to a certificate request  
    basicConstraints = CA:FALSE  
    keyUsage = nonRepudiation, digitalSignature, keyEncipherment  
    subjectAltName = @alt_names  

    [alt_names]  
	#注意这个IP.1的设置，IP地址需要和你的服务器的监听地址一样
    IP.1 = 127.0.0.1
通过服务器私钥文件和CSR文件生成服务器证书
 openssl x509 -req -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -in server-csr.pem -out server-cert.pem -extensions v3_req -extfile openssl.cnf




 格式转换



转换DER证书为PEM格式,抓HTTPS时会用到
openssl x509 -in cert.cer -inform DER -outform PEM -out cert.pem


PKCS 全称是 Public-Key Cryptography Standards ，是由 RSA 实验室与其它安全系统开发商为促进公钥密码的发展而制订的一系列标准，PKCS 目前共发布过 15 个标准。 常用的有：
PKCS#7 Cryptographic Message Syntax Standard
PKCS#10 Certification Request Standard
PKCS#12 Personal Information Exchange Syntax Standard
X.509是常见通用的证书格式。所有的证书都符合为Public Key Infrastructure (PKI) 制定的 ITU-T X509 国际标准。
PKCS#7 常用的后缀是： .P7B .P7C .SPC
PKCS#12 常用的后缀有： .P12 .PFX
X.509 DER 编码(ASCII)的后缀是： .DER .CER .CRT
X.509 PAM 编码(Base64)的后缀是： .PEM .CER .CRT
.cer/.crt是用于存放证书，它是2进制形式存放的，不含私钥。
.pem跟crt/cer的区别是它以Ascii来表示。
pfx/p12用于存放个人证书/私钥，他通常包含保护密码，2进制方式
p10是证书请求
p7r是CA对证书请求的回复，只用于导入
p7b以树状展示证书链(certificate chain)，同时也支持单个证书，不含私钥。

一 用openssl创建CA证书的RSA密钥(PEM格式)：
openssl genrsa -des3 -out ca.key 1024
二用openssl创建CA证书(PEM格式,假如有效期为一年)：
openssl req -new -x509 -days 365 -key ca.key -out ca.crt -config openssl.cnf
openssl是可以生成DER格式的CA证书的，最好用IE将PEM格式的CA证书转换成DER格式的CA证书。
三 x509到pfx
c:\openssl-1.0.0d\out32\openssl pkcs12 -export -in .\xinhr.pem -inkey .\xinhr.key -out .\xinghr.pfx
四 PEM格式的ca.key转换为Microsoft可以识别的pvk格式。
  pvk -in ca.key -out ca.pvk -nocrypt -topvk
  验证 openssl pkcs12 -clcerts -nokeys -in cert.p12 -out cert.pem
五 pfx到pem
c:\openssl-1.0.0d\out32\openssl  pkcs12  -in .\xinhr1309851947896.p12 -out .\xinhr.pem  -nodes

六 从 PFX 格式文件中提取私钥格式文件 (.key)
openssl pkcs12 -in mycert.pfx -nocerts -nodes -out mycert.key
七 转换 pem 到到 spc
openssl crl2pkcs7 -nocrl -certfile venus.pem  -outform DER -out venus.spc
用 -outform -inform 指定 DER 还是 PAM 格式。例如：
openssl x509 -in Cert.pem -inform PEM -out cert.der -outform DER
八 PEM 到 PKCS#12 的转换，
openssl pkcs12 -export -in Cert.pem -out Cert.p12 -inkey key.pem
九 从 CRT format 到 PEM
openssl x509 -in input.crt -out input.der -outform DER
openssl x509 -in input.der -inform DER -out output.pem -outform PEM




openssl x509 -in ca-cert.pem -inform PEM -out cert.der -outform DER

 openssl pkcs12 -export -in server-cert.pem -out Cert.p12 -inkey server-key.pem
