# jQuery-File-Upload cross

###### 下载地址：
https://github.com/blueimp/jQuery-File-Upload
###### 官方解决File Upload跨域地址
https://github.com/blueimp/jQuery-File-Upload/wiki/Cross-domain-uploads

   最近在做一个图片上传项目，前后端需要跨域上传图片，按照官方给出解决的跨域方案一：Cross-site XMLHttpRequest file uploads，始终不能解决跨域，于是采用了第二种方法：Cross-site iframe transport uploads来解决跨域，根据官方给出的方案，我们在前端页面新建了一个upload.html页面
   
```
<!DOCTYPE HTML>
<!--
/*
 * jQuery Iframe Transport Plugin Redirect Page
 * https://github.com/blueimp/jQuery-File-Upload
 *
 * Copyright 2010, Sebastian Tschan
 * https://blueimp.net
 *
 * Licensed under the MIT license:
 * http://www.opensource.org/licenses/MIT
 */
-->
<html lang="en">
<head>
<meta charset="utf-8">
<title>jQuery Iframe Transport Plugin Redirect Page</title>
</head>
<body>
<script>
document.body.innerText=document.body.textContent=decodeURIComponent(window.location.search.slice(1));
</script>
</body>
</html>
```

**上传页的代码是**

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<script src="jquery/jquery-1.11.2.min.js"></script>
    <script src="fileupload/jquery.ui.widget.js"></script>
    <script src="fileupload/jquery.iframe-transport.js"></script>
    <script src="fileupload/jquery.fileupload.js"></script>
    <script src="fileupload/jquery.fileupload-process.js"></script>
    <script src="fileupload/jquery.fileupload-validate.js"></script>
</head>
<body>
	<input id="fileupload" name="uploadify" type="file"  multiple="multiple" accept="image/jpeg,.jpg,image/gif,.gif,image/png,.png,.jpeg">
	<script >
		$('#fileupload').fileupload({
			url: that.prevUrl + 'upload.do'+'?group_id='+groupId,
			type:"POST",
			dataType:"json",
			autoUpload : true,
			acceptFileTypes: /(\.|\/)(gif|jpe?g|png)$/i,
			maxFileSize: 3000000, // 3 MB
			forceIframeTransport: true,
			redirectParamName:"callUrl",
			multipart: true,
			redirect:'http://10.144.32.41/upload.html?',//我本地upload.html地址
			done: function (e, data) {
				console.log(data.result);//data.result是后端传过来的数据
			},
			fail:function(e,data){
				console.log("上传失败："+data.errorThrown);
			}
        });
	</script>
</body>
</html>
```

###### 后端代码


```
string result = file.FileName;
context.Response.Headers.Add("Cache-Control", "no-cache");
context.Response.AddHeader("Access-Control-Allow-Origin", "*");
context.Response.AddHeader("Access-Control-Allow-Headers", "x-requested-with");
context.Response.AddHeader("Location", callUrl + "?msg=" + result);
context.Response.Redirect(callUrl + "?msg=" + result);
```
