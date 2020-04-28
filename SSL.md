## 自签证书

1. ##### 创建目录（放在根目录）

```
# cd ~ 
# mkdir CA && cd CA 
# mkdir private newcerts # touch index.txt 
# # echo '01' >serial
```

2. ##### 修改 OpenSSL 配置文件，使之识别 CA 目录

```
vi /etc/pki/tls/openssl.cnf
#dir=/etc/pki/CA	# Where everything is kept
dir=/root/demoCA	# 修改默认路径
```

3. ##### 生成根密钥 - RSA PRIVATE KEY

```
openssl genrsa -out cakey.pem 2048
mv cakey.pem . ./private
```

4. ##### 生成根 CA 证书（根证书一般由证书颁发机构颁发，用于自签证书则需要自己导入）

```bash
openssl req -new -x509 -days 3650 -key ./private/cakey.pem -out cacert.pem
```

5. ##### 创建请求 （与根密匙输入信息一致）

```
openssl genrsa -out ca.key 2048
openssl req -new -key ca.key -out ca.csr
```

6. ##### 附加用途

```
vim ca.ext
```

7. ##### 签发证书

```
openssl ca -in ca.csr -extfile ca.ext -days 365 -out ca.crt
```

8. ##### 添加根证书（用于自签证书）

```
cat cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt
# windows 运行certmgr.msc添加cacert.pem到信任的根证书颁发机构
```

