# PHP SDK 手册
## 文件管理

在 NOS 中，用户可以通过一系列的接口管理桶 (Bucket) 中的文件 (Object)，比如 ListObjects，DeleteObject，CopyObject，DoesObjectExist 等。

### 列出桶中的文件

您可以使用 NosClient::listObjects 列出存储中间中的文件:

    <?php
    /**
     * 列出 Bucket 内所有目录和文件， 根据返回的 nextMarker 循环调用 listObjects 接口得到所有文件
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket 存储空间名称
     * @return null
     */
    function listAllObjects($nosClient, $bucket)
    {
        for ($i = 0; $i < 100; $i += 1) {
            $nosClient->putObject($bucket, "dir/obj" . strval($i), "hi");
        }
        $prefix = 'dir/';
        $delimiter = '/';
        $nextMarker = '';
        $maxkeys = 30;
        while (true) {
            $options = array(
                'delimiter' => $delimiter,
                'prefix' => $prefix,
                'max-keys' => $maxkeys,
                'marker' => $nextMarker,
            );
            var_dump($options);
            try {
                $listObjectInfo = $nosClient->listObjects($bucket, $options);
            } catch (NosException $e) {
                printf(__FUNCTION__ . ": FAILED\n");
                printf($e->getMessage() . "\n");
                return;
            }
            var_dump($listObjectInfo);
            // 得到 nextMarker，从上一次 listObjects 读到的最后一个文件的下一个文件开始继续获取文件列表
            $nextMarker = $listObjectInfo->getNextMarker();
            $listObject = $listObjectInfo->getObjectList();
            var_dump($listObject);
            if ($nextMarker === '') {
                break;
            }
        }
    
    }

上述代码中的$options 中的参数如下所示:

|**参数**|                  **说明**               |
|--------|---------------------------------------|
|delimiter| 用于对 Object 名字进行分组的字符。所有名字包含指定的前缀且第一次出现 delimiter 字符之间的 object 作为一组元素|
|prefix|    限定返回的 object key 必须以 prefix 作为前缀。注意使用 prefix 查询时，返回的 key 中仍会包含 prefix|
|max-keys|  限定此次返回 object 的最大数，如果不设定，默认为 100|
|marker|    设定结果从 marker 之后按字母排序的第一个开始返回|

<span>Note:</span><div class="alertContent">上述表中的参数都是可选参数</div>

### 判断文件是否存在

您可以使用 NosClient::doesObjectExist 判断文件是否存在:

    <?php
    /**
     * 判断 object 是否存在
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket bucket 名字
     * @return null
     */
    function doesObjectExist($nosClient, $bucket)
    {
        $object = "nos-php-sdk-test/upload-test-object-name.txt";
        try{
            $exist = $nosClient->doesObjectExist($bucket, $object);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
        var_dump($exist);
    }

### 删除单个文件

您可以使用 NosClient::deleteObject 删除单个需要删除的文件:

    <?php
    /**
     * 删除 object
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket bucket 名字
     * @return null
     */
    function deleteObject($nosClient, $bucket)
    {
        $object = "nos-php-sdk-test/upload-test-object-name.txt";
        try{
            $nosClient->deleteObject($bucket, $object);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
    }

### 删除多个文件

您可以使用 NosClient::deleteObjects 批量删除文件:

    <?php
    /**
     * 批量删除 object
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket bucket 名字
     * @return null
     */
    function deleteObjects($nosClient, $bucket)
    {
        $objects = array();
        $objects[] = "nos-php-sdk-test/upload-test-object-name.txt";
        $objects[] = "nos-php-sdk-test/upload-test-object-name.txt.copy";
        try{
            $nosClient->deleteObjects($bucket, $objects);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
    }

### 拷贝文件

您可以使用 NosClient::copyObject 拷贝文件:

    <?php
    /**
     * 拷贝 object
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket bucket 名字
     * @return null
     */
    function copyObject($nosClient, $bucket)
    {
        $from_bucket = $bucket;
        $from_object = "nos-php-sdk-test/upload-test-object-name.txt";
        $to_bucket = $bucket;
        $to_object = $from_object . '.copy';
        try{
            $nosClient->copyObject($from_bucket, $from_object, $to_bucket, $to_object);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
    }

<span>Attention:</span><div class="alertContent">支持跨桶的文件 copy</div>

### 移动文件

您可以使用 NosClient::moveObject 移动文件:

    <?php
    /**
     * 移动 Object
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket bucket 名字
     * @return null
     */
    function copyObject($nosClient, $bucket)
    {
        $from_bucket = $bucket;
        $from_object = "nos-php-sdk-test/upload-test-object-name.txt";
        $to_bucket = $bucket;
        $to_object = $from_object . '.move';
        try{
            $nosClient->moveObject($from_bucket, $from_object, $to_bucket, $to_object);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
    }

<span>Attention:</span><div class="alertContent"> 暂时不支持跨桶的文件 move</div>

### 修改文件元信息

暂时不提供此类方法

### 获取文件的文件元信息

您可以使用 NosClient::getObjectMeta 获取对象的元数据信息:

    <?php
    /**
     * 获取 object meta, 也就是 getObjectMeta 接口
     *
     * @param NosClient $nosClient NosClient 实例
     * @param string $bucket 存储空间名称
     * @return null
     */
    function getObjectMeta($nosClient, $bucket)
    {
        $object = "nos-php-sdk-test/upload-test-object-name.txt";
        try {
            $objectMeta = $nosClient->getObjectMeta($bucket, $object);
        } catch (NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": OK" . "\n");
        if (isset($objectMeta[strtolower('Content-Disposition')]) &&
            'attachment; filename="xxxxxx"' === $objectMeta[strtolower('Content-Disposition')]
        ) {
            print(__FUNCTION__ . ": ObjectMeta checked OK" . "\n");
        } else {
            print(__FUNCTION__ . ": ObjectMeta checked FAILED" . "\n");
        }
    }

<span>Attention:</span>
获取的元数据通过一个 array 返回，返回值为 HTTP 头类型的元数据与用户自定义元数据
元数据名的大小均为小写

## 授权访问

通过生成签名 URL 的形式提供给用户一个临时的访问 URL。在生成 URL 时，您可以指定 URL 过期的时间，从而限制用户长时间访问。

生成私有下载链接 生成 GetObject 的签名 url 示例如下:

    <?php
    /**
     * 生成 GetObject 的签名 url,主要用于私有权限下的读访问控制
     *
     * @param $nosClient NosClient NosClient 实例
     * @param $bucket string bucket 名称
     * @return null
     */
    function getSignedUrlForGettingObject($nosClient, $bucket)
    {
        $object = "test/test-signature-test-upload-and-download.txt";
        $timeout = 3600; // URL 的有效期是 3600 秒
        try{
            $signedUrl = $nosClient->signUrl($bucket, $object, $timeout);
        } catch(NosException $e) {
            printf(__FUNCTION__ . ": FAILED\n");
            printf($e->getMessage() . "\n");
            return;
        }
        print(__FUNCTION__ . ": signedUrl: " . $signedUrl. "\n");
        /**
         * 可以类似的代码来访问签名的 URL，也可以输入到浏览器中去访问
         */
        $request = new RequestCore($signedUrl);
        $request->set_method('GET');
        $request->send_request();
        $res = new ResponseCore($request->get_response_header(), $request->get_response_body(), $request->get_response_code());
        if ($res->isOK()) {
            print(__FUNCTION__ . ": OK" . "\n");
        } else {
            print(__FUNCTION__ . ": FAILED" . "\n");
        };
    }

