#### 前言:  
最近在对系统的文件上传进行统一管理,发现后端的文件上传接口需要修改.原来的文件管理只是单纯的随机数+文件名为存储路径,修改的方案是不同的业务文件上传的路径为业务名+自定义分类机制(可选,可多层级)+文件名

原先的接口代码:
````
@GetMapping(value = "/files/{uuid}/{filename:.+}")
    public ResponseEntity<Resource> serveFile(@PathVariable String filename,
                                              @PathVariable String uuid) throws  Exception {
        Resource file = storageService.loadAsResource(filename, uuid);

        filename = URLDecoder.decode(filename, "UTF-8");
        filename = new String(filename.getBytes("gbk"), "iso-8859-1");
        String headerValue = "attachment; filename=" + filename;
        return ResponseEntity
                .ok()
                .header(HttpHeaders.CONTENT_DISPOSITION, headerValue)
                .body(file);
    }
   ````
   由上可见,该接口仅支持 **api/随机数/文件名** 的上传路径
 
 但修改后的文档上传接口需要满足多层的上传路径
 比如:    **api/业务名/文件名** 
  &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp; **api/业务名/用户名/文件名**    
  &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp; **api/业务名/类型/用户名/文件名**         

### 方案一 :  @GetMapping(value = "/files/{uuid:.*}/{filename:.+}")
````
@RequestMapping批注支持在URI模板变量中使用正则表达式。
语法是{varName：regex}，其中第一部分定义变量名称，第二部分定义正则表达式。
也就是说我们可以对每个变量进行正则表达式的约束
正则表达式.+表示“匹配元字符.一次或多次
````
原本想通过正则表达式让uuid可以满足多个"/"结构, 但经过测试,发现只有**api/业务名/文件名** 才能取到,其他结构均无法匹配到接口,也就是说，**变量中不能带有"/"的**。

### 方案二: 不同的'/'数量有限
 比如:    **api/业务名/文件名** 
  &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp; **api/业务名/用户名/文件名**    
  只需处理这两种结构,可以这样改
  ````
   @GetMapping(value = {"/files/{firstpath:.*}{secondpath}/{filename:.+}", "/files/{firstpath}/{secondpath}/{filename:.+}"})
    public ResponseEntity<Resource> serveFile(@PathVariable String filename,
                                              @PathVariable String firstpath,
                                              @PathVariable String secondpath) throws  Exception {
        String path = secondpath.isEmpty() ? firstpath : firstpath+"/"+secondpath;
        Resource file = storageService.loadAsResource(filename, path);
 ````
 
 ### 方案三: 层级结构多且不确定
 如:**api/XXX/XXX/XXX/XXX/XXX/XXX/XXX/XXX/文件名**
只能使用 @RequestMapping("/files/**//{filename:.+}")  来拦截住请求
然后代码里通过：
````
request.getServletPath()  
````
然后通过split("/")来拆分后取对应的内容拼接。