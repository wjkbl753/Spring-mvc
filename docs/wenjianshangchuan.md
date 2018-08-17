`导入jar包`：
```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>
```
`springmvc.xml`中配置
```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="defaultEncoding" value="utf-8"/>
    <property name="maxUploadSize" value="5000000"/>  <!--单位字节-->
</bean>
```
`表单：`
```html
<form action="${pageContext.request.contextPath}/testFileUpload" method="post" enctype="multipart/form-data">
    <input type="text" name="aa">
    <input type="file" name="file">
    <input type="submit" value="提交">
</form>
```

`controller:`
```java
@PostMapping("/testFileUpload")
public String testFileUpload(String desc, MultipartFile file, HttpServletRequest request){
    //得到上传路径的硬盘路径
    String realPath = request.getServletContext().getRealPath("/resources/upload");
    //得到文件扩展名
    String extension = file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("."));
    //新文件名
    String newFileName = UUID.randomUUID() +"."+extension;

    File file2 = new File(realPath,newFileName);
    try {
        //存文件
        file.transferTo(file2);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return "redirect:/index.jsp";
}
```


`ajax异步上传：`

使用插件：hfileuploadify(fileupload山寨版): https://github.com/Double-Lv/Huploadify

`使用步骤`:
1. 导入 hfileuploadify的css及js

2. 自己不要写file控件了,在想出现上传控件的地方按顺序添加3个div
```html
 `<input type="hidden" name="xx"/>`			(回填表单,用于提交表单的图片数据库存储)
 `<div  class="imageUpload"></div>`				(上传控件)
 `<div  class="preview"></div>`					(预览图)
```


3. js代码:
$('.imageUpload').each(function(){
    var aa = function(obj){
        obj.Huploadify({
            auto:true,
            fileTypeExts:'*.jpg;*.png;',
            multi:true,
            //formData:{name:'image0'},
            fileSizeLimit:9999,
            showUploadedPercent:true,//是否实时显示上传的百分比，如20%
            showUploadedSize:true,
            removeTimeout:9999999,
            uploader:'${pageContext.request.contextPath}/user/testFileUpload',
            onUploadComplete:function(file,data,response){
                    //获得回填数据
                data_json= JSON.parse(data)
                src = "${pageContext.request.contextPath}"+data_json.path;
                //此处obj为上传控件  ,代表每个imageUpload
                //回填表单
                obj.prev().val(data_json.path);
                //填充预览图
                obj.next().html("<img src='"+src+"' style='height:250px'/>");
                setTimeout(function(){
                    obj.find('.uploadify-queue-item').html('');
                },1000);
            }
        });
    }
    aa($(this));
});

4. 后端：
```java
@PostMapping("/testFileUpload")
@ResponseBody
public R testFileUpload(String desc, MultipartFile file, HttpServletRequest request){
    //得到上传路径的硬盘路径
    String realPath = request.getServletContext().getRealPath("/resources/upload");
    //得到文件扩展名
    String extension = file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("."));
    //新文件名
    String newFileName = UUID.randomUUID()+extension;

    File file2 = new File(realPath,newFileName);
    try {
        //存文件
        file.transferTo(file2);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return R.ok().put("path","/resources/upload/"+newFileName);
}
```

