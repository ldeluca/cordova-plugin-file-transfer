<!---
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# org.apache.cordova.file-transfer

这个插件允许你上传和下载文件。

## 安装

    cordova plugin add org.apache.cordova.file-transfer
    

## 支持的平台

*   亚马逊火 OS
*   Android 系统
*   黑莓 10 *
*   iOS
*   Windows Phone 7 和 8 *
*   Windows 8 *

**不支持 `onprogress` ，也不 `abort()` *

# 文件传输

`FileTransfer`对象提供一种方法来使用 HTTP 多部分 POST 请求的文件上传和下载文件，以及。

## 属性

*   **onprogress**： 每当一块新的数据传输的时候，使用 `ProgressEvent`来回调函数 。*(Function)*

## 方法

*   **upload**： 将文件发送到服务器。

*   **download**： 从服务器上下载文件。

*   **abort**: 取消该对象正在进行的文件传输任务。

## 上传

**Parameters**：

*   **fileURL**： 表示文件在设备上的文件系统 URL。 为向后兼容性，这也可以将设备上的文件的完整路径。 （请参见 [向后兼容性注意到] 下面)

*   **server**：接收文件的服务器地址，由`encodeURI()`编码的.

*   **successCallback**： 传递一个`FileEntry` 对象回调 。*(Function)*

*   **errorCallback**： 如果出现 `Metadata`检索错误则执行回调函数 。调用 `FileTransferError` 对象。*(Function)*

*   **trustAllHosts**: 可选参数，默认值为 `false` 。 如果设置为 `true` ，它将接受所有的安全证书。 由于Android拒绝自签名的安全证书，这是很有用的。 不建议用于生产用途。 支持 Android 和 iOS 。 *(boolean)*

*   **选项**： 可选参数*（对象）*。有效的密钥：
    
    *   **fileKey**： 表单元素的名称。默认值为 `file` 。(DOMString)
    *   **fileName**： 要保存在服务器上的文件使用的文件名称。默认值为 `image.jpg` 。 (DOMString)
    *   **mimeType**： 要上传的mime 类型的数据 。默认值为 `image/jpeg` 。(DOMString)
    *   **params**：通过HTTP请求发送到服务器的一系列可选键/值对。(Object) 
    *   **chunkedMode**： 是否上传数据块流模式。默认值为 `true` 。(Boolean)
    *   **headers**： 请求头键/值对。使用数组来指定多个值。(Object)

### 示例

    // !! Assumes variable fileURL contains a valid URL to a text file on the device,
    //    for example, cdvfile://localhost/persistent/path/to/file.txt
    
    var win = function (r) {
        console.log("Code = " + r.responseCode);
        console.log("Response = " + r.response);
        console.log("Sent = " + r.bytesSent);
    }
    
    var fail = function (error) {
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }
    
    var options = new FileUploadOptions();
    options.fileKey = "file";
    options.fileName = fileURL.substr(fileURL.lastIndexOf('/') + 1);
    options.mimeType = "text/plain";
    
    var params = {};
    params.value1 = "test";
    params.value2 = "param";
    
    options.params = params;
    
    var ft = new FileTransfer();
    ft.upload(fileURL, encodeURI("http://some.server.com/upload.php"), win, fail, options);
    

### 与上传的标头和进度事件 （Android 和 iOS 只） 的示例

    function win(r) {
        console.log("Code = " + r.responseCode);
        console.log("Response = " + r.response);
        console.log("Sent = " + r.bytesSent);
    }
    
    function fail(error) {
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }
    
    var uri = encodeURI("http://some.server.com/upload.php");
    
    var options = new FileUploadOptions();
    options.fileKey="file";
    options.fileName=fileURL.substr(fileURL.lastIndexOf('/')+1);
    options.mimeType="text/plain";
    
    var headers={'headerParam':'headerValue'};
    
    options.headers = headers;
    
    var ft = new FileTransfer();
    ft.onprogress = function(progressEvent) {
        if (progressEvent.lengthComputable) {
          loadingStatus.setPercentage(progressEvent.loaded / progressEvent.total);
        } else {
          loadingStatus.increment();
        }
    };
    ft.upload(fileURL, uri, win, fail, options);
    

## FileUploadResult

`FileUploadResult` 对象被传递到 `FileTransfer` 对象的 `upload()` 方法的成功回调。

### 属性

*   **bytesSent**：已经向服务器所上传的字节数 。(long)

*   **responseCode**： 由服务器返回的 HTTP 响应代码。(long)

*   **rseponse**： 服务器端返回的HTTP响应数据。(DOMString)

*   **标题**： 由服务器的 HTTP 响应标头。（对象）
    
    *   目前支持的 iOS 只。

### iOS 的怪癖

*   不支持 `responseCode` 或`bytesSent`.

## 下载

**参数**：

*   **source**： 要下载的文件服务器的 URL，由`encodeURI()`编码的。.

*   **目标**： 表示文件在设备上的文件系统 url。 为向后兼容性，这也可以将设备上的文件的完整路径。 （请参见 [向后兼容性注意到] 下面)

*   **successCallback**： 回调函数传递 `FileEntry` 对象。*(Function)*

*   **errorCallback**： 如果发生`Metadata`检索时出现错误，则将执行的回调 。调用`FileTransferError` 对象。*(Function)*

*   **trustAllHosts**: 可选参数，默认值为 `false` 。 如果设置为 `true` ，它可以接受的所有安全证书。 这是有用的，因为 Android 拒绝自行签名的安全证书。 不建议供生产使用。 在 Android 和 iOS 上受支持。 *(布尔值)*

*   **options**： 可选参数，目前只支持下载的文件表头 （如授权 （基本身份验证） 等）。

### 示例

    // !! Assumes variable fileURL contains a valid URL to a path on the device,
    //    for example, cdvfile://localhost/persistent/path/to/downloads/
    
    var fileTransfer = new FileTransfer();
    var uri = encodeURI("http://some.server.com/download.php");
    
    fileTransfer.download(
        uri,
        fileURL,
        function(entry) {
            console.log("download complete: " + entry.fullPath);
        },
        function(error) {
            console.log("download error source " + error.source);
            console.log("download error target " + error.target);
            console.log("upload error code" + error.code);
        },
        false,
        {
            headers: {
                "Authorization": "Basic dGVzdHVzZXJuYW1lOnRlc3RwYXNzd29yZA=="
            }
        }
    );
    

## abort

取消该正在进行的文件传输任务。Onerror 回调函数传递一个具有FileTransferError.ABORT_ERR 错误代码的 FileTransferError 对象。

### 示例

    // !! Assumes variable fileURL contains a valid URL to a text file on the device,
    //    for example, cdvfile://localhost/persistent/path/to/file.txt
    
    var win = function(r) {
        console.log("Should not be called.");
    }
    
    var fail = function(error) {
        // error.code == FileTransferError.ABORT_ERR
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }
    
    var options = new FileUploadOptions();
    options.fileKey="file";
    options.fileName="myphoto.jpg";
    options.mimeType="image/jpeg";
    
    var ft = new FileTransfer();
    ft.upload(fileURL, encodeURI("http://some.server.com/upload.php"), win, fail, options);
    ft.abort();
    

## FileTransferError

当出现一个错误的时候， `FileTransferError` 对象传递错误回调函数。

### 属性

*   **code**： 下面列出了一个预定义的错误代码。(Number)

*   **源**： 源的 URL。（字符串）

*   **目标**： 到目标 URL。（字符串）

*   **http_status**： HTTP 状态码。此属性仅适用于当一个响应从 HTTP 连接被收到时。(Number)

### 常量

*   `FileTransferError.FILE_NOT_FOUND_ERR`
*   `FileTransferError.INVALID_URL_ERR`
*   `FileTransferError.CONNECTION_ERR`
*   `FileTransferError.ABORT_ERR`

## 向后兼容性注意到

以前版本的这个插件将只接受设备绝对文件路径作为源的上载，或为下载的目标。这些路径通常将窗体的

    /var/mobile/Applications/<application UUID>/Documents/path/to/file  (iOS)
    /storage/emulated/0/path/to/file                                    (Android)
    

为向后兼容性，这些路径仍被接受，和如果您的应用程序已录得像这些持久性存储区中的路径，然后他们可以继续使用。

这些路径以前被暴露在 `fullPath` 属性的 `FileEntry` 和 `DirectoryEntry` 由文件插件返回的对象。 新版本的文件的插件，不过，不再公开这些 JavaScript 的路径。

如果您要升级到一个新的 (1.0.0 或更高版本） 版本的文件，和你以前一直在使用 `entry.fullPath` 作为的参数 `download()` 或 `upload()` ，那么您将需要更改您的代码，而使用的文件系统的 Url。

`FileEntry.toURL()`和 `DirectoryEntry.toURL()` 返回的表单文件系统 URL

    cdvfile://localhost/persistent/path/to/file
    

可以使用在中两者的绝对文件路径位置 `download()` 和 `upload()` 方法。