# AJAX上传图片

### 图片上传实现步骤

#### 图片上传

通过jquery监听input change事件，这样我们可以获取到上传的图片流信息，从而可以获取到图片的地址、大小、格式以及名称等信息

这里创建3个数组，imgName、imgSrc、imgFile分别用于存放上传图片的名称、url地址以及图片流信息

```
var fileList = this.files;
        for(var i = 0; i < fileList.length; i++) {
            var imgSrcI = getObjectURL(fileList[i]);
            imgName.push(fileList[i].name);
            imgSrc.push(imgSrcI);
            imgFile.push(fileList[i]);
        }
```

getObjectURL方法是一个用于获取本地图片的地址，使用该url可以显示图片

```
function getObjectURL(file) {
    var url = null ;
    if (window.createObjectURL!=undefined) { // basic
        url = window.createObjectURL(file) ;
    } else if (window.URL!=undefined) { // mozilla(firefox)
        url = window.URL.createObjectURL(file) ;
    } else if (window.webkitURL!=undefined) { // webkit or chrome
        url = window.webkitURL.createObjectURL(file) ;
    }
    return url ;
}
```

#### 控制上传图片大小、格式以及上传数量

```
    $('#upload').on('change',function(){        
          if(imgSrc.length==4){
            return alert("最多只能上传4张图片");
        }
        var imgSize = this.files[0].size;  //b
        if(imgSize>1024*1024*1){//1M
            return alert("上传图片不能超过1M");
        }
        if(this.files[0].type != 'image/png' && this.files[0].type != 'image/jpeg' && this.files[0].type != 'image/gif'){
            return alert("图片上传格式不正确");
        }
    })
```

#### 图片预览

创建一个addNewContent方法用于动态展示添加的图片实现图片预览，在每次上传图片的时候调用该方法

```
function addNewContent(obj) {
    $(obj).html("");
    for(var a = 0; a < imgSrc.length; a++) {
        var oldBox = $(obj).html();
        $(obj).html(oldBox + '<li class="content-img-list-item"><img src="'+imgSrc[a]+'" alt=""><a index="'+a+'" class="hide delete-btn"><i class="ico-delete"></i></a></li>');
    }
}
```

#### 图片删除

1.通过监听鼠标的mouseover事件，显示图片删除按钮

```
$('.content-img-list').on('mouseover','.content-img-list-item',function(){
        $(this).children('a').removeClass('hide');
    });
```

2.监听鼠标的mouseleave事件，隐藏图片删除按钮

```
$('.content-img-list').on('mouseleave','.content-img-list-item',function(){
        $(this).children('a').addClass('hide');
    });
```

3.获取图片index下标属性，通过js的splice方法删除数组元素，重新调用addNewContent方法遍历图片数组显示预览图片

```
$(".content-img-list").on("click",'.content-img-list-item a',function(){
            var index = $(this).attr("index");
            imgSrc.splice(index, 1);
            imgFile.splice(index, 1);
            imgName.splice(index, 1);
            var boxId = ".content-img-list";
            addNewContent(boxId);
            if(imgSrc.length<4){//显示上传按钮
                $('.content-img .file').show();
            }
      });
```

#### 图片上传提交

这里主要使用FormData来拼装好数据参数，提交到后台

```
var formFile = new FormData();
```

遍历imgFile图片流数组拼装到FormData中

```
 $.each(imgFile, function(i, file){
            formFile.append('myFile[]', file);
        });
```

添加其他参数

```
    formFile.append("type", type); 
        formFile.append("content", content); 
        formFile.append("mobile", mobile); 
```

最后使用ajax提交内容

```
 $.ajax({
            url: 'http://zhangykwww.yind123.com/webapi/feedback',
            type: 'POST',
            data: formFile,
            async: true,  
            cache: false,  
            contentType: false, 
            processData: false, 
            // traditional:true,
            dataType:'json',
            success: function(res) {
                console.log(res);
            }
        })
```

以上就实现了图片上传、图片预览和图片删除的功能

jquery设置 ajax属性

```
processData : false, // 告诉jQuery不要去处理发送的数据
contentType : false,// 告诉jQuery不要去设置Content-Type请求头
```