# Python sdk
## 开发准备

### 相关资源
-[GitHub地址](https://github.com/tencentyun/kms-python-sdk) ,欢迎贡献代码以及反馈问题。

-[python sdk 本地下载]()
### 环境依赖
python2.7 目前不支持python3

获取python版本的方法：

linux shell 

```

    $python -V

    Python 2.7.11
```

windows cmd

```
   
    D:>python -V
    Python 2.7.11
```

如果提示不是内部或者外部命令，请先在window环境变量PATH里面添加上python的绝对路径

### 历史版本

## 生成客户端对象

``` 
    secretId='xxxxxx'    #替换为用户的secretId
    secretKey = 'xxxxxx' #替换为用户的secretKey
    endpoint = 'https://kms-region.api.tencentyun.com' # 替换为用户的region , 例如 sh 表示上海， gz表示广州，bj表示北京
    kms_account = KMSAccount(endpoint,secretId,secretKey)
```
### 初始化客户端配置
客户端默认使用sha1 签名算法，可以调用签名算法修改签名方式

```
    kms_account.set_sign_method('sha256')
```

## 密钥管理操作
### 创建主密钥
#### 方法原型

```
    def create_key(self, Description=None, Alias="", KeyUsage='ENCRYPT/DECRYPT')
```

#### 参数说明

| 参数名 | 类型 | 默认值 | 参数描述 |
|---------|---------|---------|---------|
|Description|string|None|主密钥描述|
|Alias|string|空字符串|主密钥别名|
|KeyUsage|string|'ENCRYPT/DECRYPT'|主密钥用途：默认是加解密|

返回值 KeyMetadata结构体 描述如下：

| 属性名称 | 类型 | 含义 |
|---------|---------|---------|
|KeyId|string|密钥id|
|CreateTime|uinx time|创建时间|
|Description|string|密钥描述|
|KeyState|string|密钥状态|
|KeyUsage|string|密钥用途|
|Alias|string|密钥别名|

#### 使用示例

```
    description ='for test'
    alias = 'kms_test'
    kms_meta = kms_account.create_key(description,alias)
```

### 获取主密钥属性
#### 方法原型

```
    def get_key_attributes(self, KeyId=None)
```

#### 参数说明

| 参数名 | 类型 | 默认值 | 参数描述 |
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id|

返回值 KeyMetadata结构体 描述如下：

| 属性名称 | 类型 | 含义 |
|---------|---------|---------|
|KeyId|string|密钥id|
|CreateTime|uinx time|创建时间|
|Description|string|密钥描述|
|KeyState|string|密钥状态|
|KeyUsage|string|密钥用途|
|Alias|string|密钥别名|

#### 使用示例

```
    keyId=''  # 请填写你的keyId
    key_meta = kms_account.get_key_attributes("kms-awy8dndb")
    print key_meta
```

### 设置主密钥属性
#### 方法原型

```
    def set_key_attributes(self, KeyId=None, Alias=None)
```

#### 参数说明

| 参数名 | 类型 | 默认值 | 参数描述 |
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id|
|Alias|string|None|主密钥别名|

返回值 无

#### 使用示例

```
    keyId=''  # 请填写你的keyId
    Alias=''  # 请填写你的主密钥别名
    kms_account.get_key_attributes(keyId,Alias)
```

### 获取主密钥列表
#### 方法原型

```
    def list_key(self, offset=0, limit=10)
```

#### 参数说明

| 参数名 | 类型 | 默认值 | 参数描述 |
|---------|---------|---------|---------|
|offset|int|0|返回列表偏移值。|
|limit|int|10|本次返回列表限制个数，不填写默认为返回10个。|

返回值 KeyMetadata结构体 描述如下：

|属性名称|类型|含义|
|---------|---------|---------|
|totalCount|int|表示所有的密钥个数。|
|keys|array|key数组。|

#### 使用示例

```
    totalCount, keys = kms_account.list_key()
    print keys
```
### 生成数据密钥
#### 方法原型

```
    def generate_data_key(self, KeyId=None, KeySpec=None, NumberOfBytes=None, EncryptionContext=None)
```

#### 参数说明

|参数名|类型|默认值|参数描述|
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id。|
|KeySpec|string|None|生成数据密钥算法。|
|NumberOfBytes|int|None|生成指定长度的数据密钥。|
|EncryptionContext|string|None|生成数据密钥时提供的额外的json key-value。|

返回值 

|参数名|类型|参数描述|
|---------|---------|---------|
|plaintext|string| 表示生成的数据密钥明文|
|ciphertextBlob|string|表示生成的数据密钥密文|


#### 使用示例

```
        KeySpec = "AES_128"
        Plaintext, CiphertextBlob = kms_account.generate_data_key(KeyId, KeySpec)
        print "the data key : %s \n  the encrypted data key :%s\n" % (Plaintext, CiphertextBlob)
```
### 启用主密钥
#### 方法原型

```
    def enable_key(self, KeyId=None)
```

#### 参数说明

|参数名|类型|默认值|参数描述|
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id|

返回值 无

#### 使用示例

```
    kms_account.enable_key(KeyId)
```
### 禁用主密钥
#### 方法原型

```
    def disable_key(self, KeyId=None)
```

#### 参数说明

|参数名|类型|默认值|参数描述|
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id|

返回值 无
#### 使用示例

```
    kms_account.disable_key(KeyId)
```

## 加解密操作
### 加密
#### 方法原型

```
    def encrypt(self, KeyId=None, Plaintext="", EncryptionContext=None)
```

#### 参数说明

|参数名|类型|默认值|参数描述|
|---------|---------|---------|---------|
|KeyId|string|None|主密钥Id|
|Plaintext|string|空字符串|明文|
|EncryptionContext|string|None|key/value对的json字符串，如果指定了该参数，则在调用Decrypt API时需要提供同样的参数。|

返回值：

|参数名|类型|参数描述|
|---------|---------|---------|
|ciphertextBlob|string|表示生成的密文|
#### 使用示例

```
    Plaintest = "test message data"
    CiphertextBlob = kms_account.encrypt(kms_meta.KeyId, Plaintest)
    print "the encrypted data is :%s \n" % CiphertextBlob
```
### 解密
#### 方法原型

```
    def decrypt(self, CiphertextBlob="", EncryptionContext=None)
```

#### 参数说明

|参数名|类型|默认值|参数描述|
|---------|---------|---------|---------|
|CiphertextBlob|string|空字符串|密文|
|EncryptionContext|string|None|key/value对的json字符串，如果指定了该参数，则在调用Decrypt API时需要提供同样的参数。|

返回值 ：

|参数名|类型|参数描述|
|---------|---------|---------|
|plaintext|string|表示通过密文解密得到的明文|
#### 使用示例

```
    Plaintest = kms_account.decrypt(CiphertextBlob)
    print "the decrypted data is :%s\n" % Plaintest
```



