---
title: img的上传、预览和FileReader类
date: 2021-03-17 12:59:56
tags:
  - js 原生
categories:
  - js
---

### 原生js实现图片上传预览

  1. 先简单看一下图片的上传和预览的方式

  ```
  <label for="file">上传图片</label>
  <input id="file" type="file" name="file" onchange="changepic()"></input>
  <img src="" id="show" width="200">
  <script>
    function changepic() {
      const reads = new FileReader();
      const f = document.querySelector('#file').files[0];
      reads.readAsDataURL(f);
      reads.onload = function (e) {
          document.getElementById('show').src = this.result;
      };
    }
  </script>

  ```


###  js中FileReader()用法

  HTML5定义了FileReader作为文件API的重要成员用于读取文件，根据W3C的定义，FileReader接口提供了读取文件的方法和包含读取结果的事件模型。

  FileReader的使用方式非常简单，可以按照如下步骤创建FileReader对象并调用其方法：
    1. 检测浏览器对FileReader的支持
    ```
    if(window.FileReader) {
    var fr = new FileReader();
    // add your code here
    }
    else {
        alert("Not supported by your browser!");
    }
    ```

    2. 调用FileReader对象的方法
    FileReader 的实例拥有 4 个方法，其中 3 个用以读取文件，另一个用来中断读取。下面的表格列出了这些方法以及他们的参数和功能，需要注意的是 ，无论读取成功或失败，方法并不会返回读取结果，这一结果存储在 result属性中。
        + abort() :中断读取
        + readAsBinaryString(fileobj) :将文件读取为二进制码
        + readAsDataURL(fileobj) :将文件读取为 DataURL
        + readAsText(fileobj,[encoding]) :将文件读取为文本
    readAsText ：该方法有两个参数，其中第二个参数是文本的编码方式，默认值为 UTF-8。这个方法非常容易理解，将文件以文本方式读取，读取的结果即是这个文本文件中的内容。
    readAsBinaryString ：该方法将文件读取为二进制字符串，通常我们将它传送到后端，后端可以通过这段字符串存储文件。
    readAsDataURL ：这是例子程序中用到的方法，该方法将文件读取为一段以 data: 开头的字符串，这段字符串的实质就是 Data URL，Data URL是一种将小文件直接嵌入文档的方案。这里的小文件通常是指图像与 html 等格式的文件。
    3. 处理事件
    FileReader 包含了一套完整的事件模型，用于捕获读取文件时的状态，下面这个表格归纳了这些事件。
      + onabort 中断时触发
      + onerror  出错时触发
      + onload  文件读取成功完成时触发
      + onloadend 读取完成触发，无论成功或失败
      + onloadstart 读取开始时触发
      + onprogress 读取中
    文件一旦开始读取，无论成功或失败，实例的 result 属性都会被填充。如果读取失败，则 result 的值为 null ，否则即是读取的结果，绝大多数的程序都会在成功读取文件的时候，抓取这个值。
    ```
    fr.onload = function() {
        this.result;
    }
    ```

    4. 下面通过一个上传图片预览和带进度条上传来展示FileReader的使用。
    ```
    <script type="text/javascript">
        function showPreview(source) {
          var file = source.files[0];
          if(window.FileReader) {
            var fr = new FileReader();
            fr.onloadend = function(e) {
              document.getElementById("portrait").src = e.target.result;
            };
            fr.readAsDataURL(file);
          }
        }
    </script>
    
    <input type="file" name="file" onchange="showPreview(this)" />
    <img id="portrait" src="" width="70" height="75">
    ```

    如果要限定上传文件的类型，可以通过文件选择器获取文件对象并通过type属性来检查文件类型
    ```
    if(!/image\/\w+/.test(file.type)){
        alert("请确保文件为图像类型");
        return false;
    }
    ```

    不难发现这个检测是基于正则表达式的，因此可以进行各种复杂的匹配，非常有用。
    如果要增加一个进度条，可以使用HTML 5的progress标签，通过下面的代码实现。
    ```
    <form>
        <fieldset>
            <legend>分度读取文件：</legend>
            <input type="file" id="File" />
            <input type="button" value="中断" id="Abort" />
            <p>
                <label>读取进度：</label><progress id="Progress" value="0" max="100"></progress>
            </p>
            <p id="Status"></p>
        </fieldset>
    </form>
    ```

    ```
    var h = {
    init: function() {
        var me = this;
         
        document.getElementById('File').onchange = me.fileHandler;
        document.getElementById('Abort').onclick = me.abortHandler;
         
        me.status = document.getElementById('Status');
        me.progress = document.getElementById('Progress');
        me.percent = document.getElementById('Percent');
         
        me.loaded = 0;
        //每次读取1M
        me.step = 1024 * 1024;
        me.times = 0;
        },
        fileHandler: function(e) {
            var me = h;
            
            var file = me.file = this.files[0];
            
            var reader = me.reader = new FileReader();
            
            //
            me.total = file.size;
            
            reader.onloadstart = me.onLoadStart;
            reader.onprogress = me.onProgress;
            reader.onabort = me.onAbort;
            reader.onerror = me.onerror;
            reader.onload = me.onLoad;
            reader.onloadend = me.onLoadEnd;
            //读取第一块
            me.readBlob(file, 0);
        },
        onLoadStart: function() {
            var me = h;
        },
        onProgress: function(e) {
            var me = h;
            
            me.loaded += e.loaded;
            //更新进度条
            me.progress.value = (me.loaded / me.total) * 100;
        },
        onAbort: function() {
            var me = h;
        },
        onError: function() {
            var me = h;
            
        },
        onLoad: function() {
            var me = h;
    
            if(me.loaded < me.total) {
                me.readBlob(me.loaded);
            } else {
                me.loaded = me.total;
            }
        },
        onLoadEnd: function() {
            var me = h;
            
        },
        readBlob: function(start) {
            var me = h;
            
            var blob,
                file = me.file;
            
            me.times += 1;
            
            if(file.webkitSlice) {
                blob = file.webkitSlice(start, start + me.step + 1);
            } else if(file.mozSlice) {
                blob = file.mozSlice(start, start + me.step + 1);
            }
            
            me.reader.readAsText(blob);
        },
        abortHandler: function() {
            var me = h;
            
            if(me.reader) {
                me.reader.abort();
            }
        }
    };
    
    h.init();

    ```