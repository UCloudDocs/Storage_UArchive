
## 介绍(Introduction)

Uarchive是UCLOUD公司的归档存储产品，你可以把本地的任意文件，压缩包，或者文件夹上传到Uarchive。  
稍后在您需要时可以从Uarchive下载指定的文件。Uarchive和Ufile不同，它无法在UCLOUD官方控制台上进行文件的上传，下载动作。
只能依赖于uarchivemgr工具进行相关操作。你可以从下面这个地址获取这个工具。

### Linux 64位操作系统请下载

<http://tools.ufile.ucloud.cn/uarchivemgr-linux64.tar.gz>

### Windows 32位操作系统请下载

<http://tools.ufile.ucloud.cn/uarchivemgr-win32.tar.gz>

### Windows 64位操作系统请下载

<http://tools.ufile.ucloud.cn/uarchivemgr-win64.tar.gz>

### MacOS 操作系统请下载

<http://tools.ufile.ucloud.cn/uarchivemgr-mac.tar>

\# 名词定义(Definition) 这里我们将介绍一些名词，稍后在使用uarchivemgr工具的过程中，您将会看到他们。
工具中将只显示英文，为了方便您的理解，我们特别将关键词都以中文对照以及解释的方式列在下面这里，方便参考。

| 序号(Index) | 英文(English)      | 中文(Chinese) | 工具当中的选项(Corresponding Options in "uarchivemgr") | 释义(Explaination)                                                                                                                                                                                         |
| --------- | ---------------- | ----------- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0         | Vault            | 仓库          | \\--vault                                       | 仓库(Vault)是归档存储用来管理所有上传文件的空间, 通过创建不同的仓库(Vault)，您可以方便的对文件按类别进行管理，当然，您也可以将所有文件放在一个仓库(Vault)中。另外，仓库(Vault)可以创建在不同的地域(Region)，从而可以降低您连接到该仓库(Vault)的延迟。在上传文件到归档存储的过程中，您必须指定一个仓库(Vault)的名字，该仓库(Vault)必须是已经提前创建好 |
| 1         | Archive          | 存档          | \\--archive                                     | 存档(Archive)是您位上传的文件指定的存档名称，您可以指定成与上传文件名相同的名称。如果您需要将一个文件的多个不同修订版本上传到同一个仓库(Vault)，您可以使用这个选项来指定不同的存档名称。从某种程度上讲，存档(Archive)约等于文件(File)。                                                                      |
| 2         | Dir or Directory | 文件夹/目录      | \\--dir                                         | 指本地计算机的一个文件夹                                                                                                                                                                                             |
| 3         | File             | 文件          | \\--file                                        | 指本地的一个文件，可以是相对路径，也可以是绝对路径                                                                                                                                                                                |
| 4         | Activate         | 激活          | ActivateArchive                                 | 激活一个已经上传到归档存储中的存档(Archive)，使其变得可以下载。注意：默认上传到归档存储中的文件都是不可以直接下载的，如果需要下载，必须要先使用这个功能对其进行激活                                                                                                                   |
| 5         | Download         | 下载          | DownloadArchive                                 | 下载一个已经激活的存档(Archive)。默认为流式下载，对比较小的文件比较友好                                                                                                                                                                 |
| 6         | MDownload        | 分片下载        | MDownloadArchive                                | 使用分片的方式下载一个已经激活的存档(Archive)，当您的存档比较大的时候，例如数百兆或者数G，您应该使用这种方式下载，可以节约大量时间                                                                                                                                   |
| 7         | Put              | 上传          | PutArchive                                      | 上传一个文件到归档存储，使用流式上传，上传小文件(例如几兆的)比较友好                                                                                                                                                                      |
| 8         | MPut             | 分片上传        | MPutArchive                                     | 使用分片的方式上传一个文件到归档存储，如果文件大小比较大，使用此方式                                                                                                                                                                       |
| 9         | Sync             | 同步          | Sync                                            | 同步一个本地文件夹到归档存储，如果这个文件夹中的文件还没有被上传到归档存储，这些文件会被上传，如果是已经上传到归档存储，那么会使用本地的文件覆盖归档存储中的文件                                                                                                                         |
| 10        | ETag             | 电子标签        | ETag                                            | 计算本地文件的电子标签。电子标签是对本地文件的数据进行的一个总结，使用MD5或者SHA算法进行计算，您可以通过对比本地文件的ETag和归档存储中文件的ETag来判断文件在上传过程中是否损坏                                                                                                           |

\# 工具配置(Configuration)
下载工具并解压缩之后，您需要编辑config.cfg文件并指定正确的公钥(PublicKey)和私钥(PrivateKey)。如果您不知道什么是公钥和私钥，请联系技术支持。

\# 工作流程(Workflow)

```
开始(Start) ---> 创建仓库(Create Vault)
                        |--> 上传文件或目录存档(Put File or Dir to Uarchive)
                        |
                        |--> 获取存档列表(Get Archive List)
                        |
                        |--> 激活存档(Activate Archive)
                                      |---> 下载存档(Download Archive)
                        |--> 删除存档(Delete Archive)

结束(End) ---> 删除仓库(Delete Vault)
```

\# 选项和示例(Options and Examples)
基本的工具选项将被列在下面，当您理解了这些选项之后，可以通过帮助(Help)来获取高级选项信息。

\# 获取工具版本(Version)

```
./uarchivemgr-linux64 version
```

\# 仓库管理(Vault Management)

```
# 创建一个仓库(Vault), 必须要指定--vault, 其后是该仓库的名称, 这个名称在后面操作会被作为参数传递给这个工具
./uarchivemgr-linux64 CreateVault --vault test-u2

# 删除一个仓库(Vault), 必须要指定--vault, 当您不再需要这个仓库时, 可以使用下面这行命令来执行删除, 现在先跳过这个命令
# ./uarchivemgr-linux64 DeleteVault --vault test-u2

# 获取仓库(Vault)信息, 可以选择指定--vault或不指定，如果指定，则显示某一个仓库(Vault)的信息，如果不指定，则显示全部。只会显示用户自己的仓库(Vault)信息。备注: 当前版本, 指定仓库(--vault)选项无法正常工作
./uarchivemgr-linux64 DescribeVault [--vault test-u2]
```

\# 上传存档(PutArchive)

```
# 上传一个文件(File)，必须要指定--vault, --file, --archive三个参数，其中--file后面是一个合法的本地文件路径，--archive后面是您指定的存档名称，这个参数在当前版本的工具中不可以省略，您可以将它指定为文件的名字
./uarchivemgr-linux64 PutArchive --vault test-u2 --file /root/config.cfg --archive config.cfg

# 上传一个文件夹(Dir), 必须指定--vault, --dir两个参数, 多余的参数将会被忽略. 其中--dir后面是一个合法的本地文件夹的地址, 该文件夹下所有的文件(File)会被上传到归档存储, 请参考--trimpath的说明来获取更多信息
./uarchivemgr-linux64 PutArchive --vault test-u2 --dir /home/marvin/uarchive-test
```

\# 获取存档列表(GetArchiveList)

```
使用这个功能，您可以获取一个仓库的存档列表。也就是您上传的文件的列表，如果一个文件的多个版本在上传的时候指定了不同的存档名称，那么您将看到它们。
# test-u2是一个合法且存在的仓库(Vault)名称，您需要将它替换为您自己的仓库名称，在后续的所有例子中，都会使用"test-u2"作为测试仓库。必须指定--vault选项，该选项后面是仓库的名称，下面的例子里面也会使用这个参数将不再单独解释
./uarchivemgr-linux64 GetArchiveList --vault test-u2
# 这个命令会输出下面这样的一个输出， 其中Key的值是后面激活存档(ActivateArchive)功能需要的参数
{
        VaultName:    test-u2
        Key:          config.cfg
        Hash:         AQAAALYX2XqoLlhkvFiBes1z04GAXbT0
        MimeType:     application/octet-stream
        Size:         278
        Created:      1556435482
        Modified:     1556435482
}
```

\# 激活存档(ActivateArchive)

```
# 激活一个存档(Archive)，必须指定--vault和--archive，其中--archive后面指定存档(Archive)的Key, 这个Key可以在GetArchiveList的返回列表中看到。
./uarchivemgr-linux64 ActivateArchive --vault test-u2 --archive config.cfg
```

\# 下载存档(DownloadArchive)

```
# 下载一个激活的存档(Archive)，必须指定--vault, --archive, --saveas。其中--archive后面指定一个已经被激活的存档的Key，和上面一条命令中输入的参数相同；--saveas参数后面指定您期望将这个存档保存到本地的文件路径。请确定您输入的文件路径在本地不存在，如果是本地已存在的文件，这个文件将会被覆盖
./uarchivemgr-linux64 DownloadArchive --vault test-u2 --archive config.cfg --saveas ./config.cfg.bk
# 如果您不是想下载这个存档(Archive)，而只想获得这个存档(Archive)的URL，您可以使用如下命令，会显示它的URL，文件并不会被下载。请注意，在当前版本--saveas参数不能省略，这是一个BUG，我们将在稍后的版本修正它
./uarchivemgr-linux64 DownloadArchive --vault test-u2 --archive config.cfg --saveas ./config.cfg --showurl
```

\# 删除存档(DownloadArchive)

```
# 删除一个存档(Archive), 必须指定--vault, --archive。
./uarchivemgr-linux64 DeleteArchive --vault test-u2 --archive config.cfg
```

\#同步文件夹(Sync)

```
# 同步一个文件夹下的所有内容到服务器端，必须指定--vault和--dir，其中--dir后面指定一个本地的文件夹路径，可以是相对路径也可以是绝对路径。另外，由于在同步文件夹过程中，无法为每一个文件指定对应的存档名称(Archive Key)，工具会默认为文件夹中的每一个文件使用该文件的绝对路径作为对应的存档名称(Archive Key)
./uarchivemgr-linux64 Sync --vault test-u2 --dir /root/testdir/
# 执行该命令之后得到的列表是下面这个样子，其中每个存档(Archive)的Key都是该文件在本地的绝对路径。注意: 服务端只使用这个路径字符串作为标识符，并不会真的用这个路径来保存存档(Archive)。
{
        VaultName:    test-u2
        Key:          /home/marvin/uarchieve/uarchivemgr_linux64.elf/config.cfg
        Hash:         AQAAALYX2XqoLlhkvFiBes1z04GAXbT0
        MimeType:     application/octet-stream
        Size:         278
        Created:      1556440367
        Modified:     1556440367
}

{
        VaultName:    test-u2
        Key:          /home/marvin/uarchieve/uarchivemgr_linux64.elf/uarchivemgr-linux64
        Hash:         AwAAAEBBQhnkmBulZ0iKMnslstJQ-qat
        MimeType:     application/octet-stream
        Size:         9456469
        Created:      1556440370
        Modified:     1556440370
}
```

\# 电子签名(ETag)

```
# 这个功能用来计算本地文件的电子签名(ETag)，通过对比GetArchiveList中得到的已上传存档的电子签名(ETag)，可以方便的知道文件上传过程中有没有出错。必须指定--file选项，其后为本地文件路径
./uarchivemgr-linux64 ETag --file ./config.cfg
```

\# 获得帮助(Help)

```
# 对于所有的命令，您都可以使用help来获取对应的帮助信息，目前只支持英文版本
./uarchivemgr-linux64 version --help
./uarchivemgr-linux64 GetArchiveList --help
./uarchivemgr-linux64 DeleteVault --help
./uarchivemgr-linux64 DescribeVault --help
./uarchivemgr-linux64 CreateVault --help
./uarchivemgr-linux64 DeleteArchive --help
./uarchivemgr-linux64 ActivateArchive --help
./uarchivemgr-linux64 DownloadArchive --help
./uarchivemgr-linux64 MDownloadArchive --help
./uarchivemgr-linux64 PutArchive --help
./uarchivemgr-linux64 MputArchive --help
./uarchivemgr-linux64 Sync --help
./uarchivemgr-linux64 ETag --help
```

\# 高级功能(Advanced Function)

```
# 在使用Put或Sync功能上传文件夹时，可以通过指定--prefix来指定文件前缀，只有文件的前缀与该选项所指定的前缀相同的文件会被上传。例如下面的命令，会上传/home/marvin/uarchive-test文件夹下所有前缀为demo的文件
./uarchivemgr-linux64 PutArchive --vault test-u2 --dir /home/marvin/uarchive-test --prefix demo

# 在使用Put或Sync功能上传文件夹时，可以使用--followsym选项来上传符号链接(Symbol Link)所指向的文件，而不是符号链接本身
./uarchivemgr-linux64 PutArchive --vault test-u2 --dir /home/marvin/uarchive-test --followsym

# 在使用Put或Sync功能上传文件夹时，默认会使用文件的全路径作为存档名(Archive Key)，可以使用--trimpath来修改这个行为。如果附带了--trimpath这个选项，那么将会使用文件的名字(File Name)而不是全路径(Full Path)作为存档名(Archive Key)
./uarchivemgr-linux64 PutArchive --vault test-u2 --dir /home/marvin/uarchive-test --trimpath

# 更多其他功能请参考帮助(Help)
```
