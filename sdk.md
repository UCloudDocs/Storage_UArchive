# SDK

**注：UArchive 产品已作为“归档存储”类型合并至 US3 对象存储，目前不再向新用户提供独立的归档存储服务。**
如需使用更低成本的对象存储服务，请至 [US3 对象存储控制台](https://console.ucloud.cn/ufile/ufile) 。更多 US3 归档存储类型的使用说明请参考 [数据归档方案](https://docs.ucloud.cn/ufile/solutions/archive)。

本文以Python SDK为例，介绍如何使用SDK进行空间管理与文件管理。本文建议用户使用Python 2。

本文使用到的SDK-Python下载地址：
[SDK-Python](http://testsign2.ufile.ucloud.com.cn/uarchive-python.zip)

本源码包含使用 Python 对 UCloud 的归档存储业务进行空间和内容管理的 API，适用于 Python 2

## 依赖的 Python Package

**requests**  
**unittest**（仅test\_uarchive目录下测试文件依赖）

## 文件目录说明

  - uarchive文件夹： SDK 的具体实现
  - setup.py: package 安装文件
  - setup.py: package 请求源设置（这里设置为阿里云的源）
  - test\_uarchive 文件夹: 测试文件以及 demo 示例

## 使用方法

执行 `%%python setup.py install%%` 命令，安装为 egg 包后使用

## 快速准备

安装成功后，以文档上传为例，进入 test\_uarchive 文件夹，预先假定您已经在控制台创建归档存储空间。  
接下来设置 UARCHIEVE\_PUBLIC\_KEY,UARCHIEVE\_PRIVATE\_KEY,UARCHIEVE\_VAULT
环境变量。 环境变量不同的操作系统有不同的设置方法。在使用 bash 的 shell 里面可以直接 `%%export
UARCHIEVE_VAULT="abcefg"%%`。

每个 test 脚本里面拿到 public\_key, private\_key, vault 这三个配置相关的环境变量。如下所示：

``` python
public_key = os.getenv("UARCHIEVE_PUBLIC_KEY") 
private_key = os.getenv("UARCHIEVE_PRIVATE_KEY")
#再控制台创建好空间后，把名字填入这里。
vault = os.getenv("UARCHIEVE_VAULT")
```

环境变量配置完成后执行 `%%python test_putarchive.py%%`  
所有测试用例执行后，如果打印 OK，恭喜您，环境已经准备完毕，可以开始您的 SDK 使用之旅啦！

**注意：部分测试用例存在依赖关系：** 删除和激活存档测试用例要先执行上传存档。

## 功能说明

### 设置其他参数

``` python
#from uarchive import config
#设置上传host后缀,外网可用 .uarchive.ucloud.cn
config.set_default(uploadsuffix='YOUR_UPLOAD_SUFFIX')
#设置下载host后缀，比如CDN下载 .uarchive.ucloud.com.cn
config.set_default(downloadsuffix='YOUR_DOWNLOAD_SUFFIX')
#设置请求连接超时时间，单位为秒
config.set_default(connection_timeout=60)
#设置私有vault下载链接有效期,单位为秒
config.set_default(expires=60)
```

### 设置日志文件

``` python
#from uarchive import logger
locallogname = '' #完整本地日志文件名
logger.set_log_file(locallogname)
```

### HTTP 返回码说明

| 状态码 | 描述         |
| --- | ---------- |
| 200 | 文件或者数据上传成功 |
| 400 | 上传到不存在的空间  |
| 401 | 上传凭证错误     |
| 403 | API公私钥错误   |
| 404 | 下载文件或数据不存在 |
| 416 | 文件范围请求不合法  |

### 获取文件列表

``` python
from uarchive import getarchivefilelist
getarchivefilelist_handler = getarchivefilelist.GetArchiveFileList(public_key, private_key)

#通过 marker 循环获取文件列表，marker为空，从头拉取文件列表
marker = ""
prefix = "*"
vault = "hello"
ret, resp = getarchivefilelist_handler.getarchivefilelist(vault, prefix, marker)
assert resp.status_code == 200
logger.info(resp.content)
assert len(ret['DataSet']) != 0
for item in ret['DataSet']:
    key = item['ArchiveName'].encode('utf-8')
    logger.info(key)
nextMarker = ret['NextMarker']
logger.info('NextMarker is {0}'.format(nextMarker))
```

### 普通上传

``` python
localfile = ''          #本地文件名
put_key = ''            #上传文件在空间中的名称

from uarchive import putarchive

putarchive_handler = putarchive.PutArchive(public_key, private_key)

# 普通上传文件至归档存储空间
ret, resp = putarchive_handler.putfile(vault, put_key, localfile, header=None)
assert resp.status_code == 200

# 普通上传二进制数据流至归档存储空间
from io import BytesIO
bio = BytesIO(u'你好'.encode('utf-8'))  #二进制数据流
stream_key = ''                         #上传数据流在空间中的名称
ret, resp = putarchive_handler.putfile(vault, stream_key, bio)
```

### 文件下载

``` python
public_savefile = ''        #保存文件名
private_savefile = ''       #保存文件名
range_savefile = ''     #保存文件名
put_key = ''            #文件在空间中的名称
stream_key = ''         #文件在空间中的名称

from uarchive import downloadarchive

downloadarchive_handler = downloadarchive.DownloadArchive(public_key, private_key)

# 从归档存储空间下载文件
ret, resp = downloadarchive_handler.download_archive(vault, put_key, private_savefile)
assert resp.status_code == 200
```

### 删除文件

``` python
vault = ''               #归档存储空间名称
delete_key = ''          #文件在空间中的名称

from uarchive import deletearchive

deletearchive_handler = deletearchive.DeleteArchive(public_key, private_key)

# 删除归档存储空间的文件
ret, resp = deletearchive_handler.delete_archive(vault, delete_key)
assert resp.status_code == 204
```

### 分片上传和断点续传

``` python
vault = ''           #归档存储空间名称
sharding_key = ''       #上传文件在空间中的名称
localfile = ''          #本地文件名

from uarchive import multipartuploadarchive

multipartuploadarchive_handler = multipartuploadarchive.MultipartUploadArchive(public_key, private_key)

# 分片上传一个全新的文件
ret, resp = multipartuploadarchive_handler.uploadfile(vault, sharding_key, localfile)
while True:
if resp.status_code == 200:     # 分片上传成功
    break
elif resp.status_code == -1:    # 网络连接问题，续传
    ret, resp = multipartuploadarchive_handler.resumeuploadfile()
else:   # 服务或者客户端错误
    print(resp.error)
    break

# 分片上传一个全新的二进制数据流
from io import BytesIO
bio = BytesIO(u'你好'.encode('utf-8'))
ret, resp = multipartuploadarchive_handler.uploadstream(vault, sharding_key, bio)
while True:
if resp.status_code == 200:     # 分片上传成功
    break
elif resp.status_code == -1:    # 网络连接问题，续传
    ret, resp = multipartuploadarchive_handler.resumeuploadstream()
else:   # 服务器或者客户端错误
    print(resp.error)
    break
```

### 空间管理

``` python
from uarchive import vaultmanager
vaultmanager_handler = vaultmanager.VaultManager(public_key, private_key)

# 创建新的vault
vaultname = '' #创建的空间名称
ret, resp = vaultmanager.createvault(vaultname)
assert resp.status_code == 200

# 删除vault
vaultname = '' #待删除的空间名称
ret, resp = vaultmanager.deletevault(vaultname)
print(ret)

# 获取vault信息
vaultname = '' #待查询的空间名称
ret, resp = vaultmanager.describevault(vault)
print(ret)
```
