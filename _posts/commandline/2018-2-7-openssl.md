---
title: Mac命令行：openssl
categories: [命令行]
---

## HTTPS

### 1.创建自己的CA机构

为CA生成私钥

```shell
openssl genrsa -out ca-key.pem -des 1024
```

通过CA私钥生成CSR

```shell
openssl req -new -key ca-key.pem -out ca-csr.pem
```

通过CSR文件和私钥生成CA证书

```shell
openssl x509 -req -in ca-csr.pem -signkey ca-key.pem -out ca-cert.pem
```


### 3.创建服务器端证书

为服务器生成私钥

```shell
 openssl genrsa -out server-key.pem 1024
```

利用服务器私钥文件服务器生成CSR

```shell
 openssl req -new -key server-key.pem -config openssl.cnf -out server-csr.pem
```

这一步非常关键，你需要指定一份openssl.cnf文件。可以用这个

```
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
    basicConstraints = CA:FALSE  
    keyUsage = nonRepudiation, digitalSignature, keyEncipherment  
    subjectAltName = @alt_names  

    # 要匹配的域名，可以做二级域名通配
    [alt_names]  
    IP.1 = 127.0.0.1
    DNS.1 = *.domain.net

```

通过服务器私钥文件和CSR文件生成服务器证书

```shell
 openssl x509 -req -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -in server-csr.pem -out server-cert.pem -extensions v3_req -extfile openssl.cnf
```

## 格式转换

DER -> PEM

```shell
openssl x509 -in cert.cer -inform DER -outform PEM -out cert.pem
```

PEM -> DER

```shell
openssl x509 -in ca-cert.pem -inform PEM -out cert.der -outform DER
```

打包p12

```shell
openssl pkcs12 -export -in server-cert.pem -out cert.p12 -inkey server-key.pem
```
