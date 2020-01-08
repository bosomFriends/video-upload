## 视频上传
##### 最近项目开发的过程中老板需要做一个视频上传带有进度条的功,并且希望上传的进度是符合真实的情况,这样就需要前端和后端配合,以往的视频上传做的进度条都是前端这边处理这种处理是用axios请求里面onUploadProgress的progressEvent来获取已上传文件大小和别上传文件的大小来达到进度的效果,但是这种只是前端将数据传到后端的数据库,并没有传到服务器上面,这种上传的效果是不符合老板的要求,并且视频要考虑到视频的大小,如果超过了2G这种大型的文件用之前的传法也是会比较慢的,所以现在我研究一下分片的上传的原理,利用分片上传这样速度效率也会提高,并且即使可以暂停上传再次重新上传,不会影响效果,以前我们常规操作就是实现不了这种效果。废话不多说,请你们来使用一下这个demo.

#### 视频上传的踩坑点
#### 1.视频上传时基于百度的WebUploader技术来实现的，然而这个插件的技术它时基于jquery来实现,所以在我们利用vue开发项目使用这个技术的过程我们需要引入jquery这个包,并且同时我们引入WebUploader它里面需要引入的它自带的一些安装的包,利于css和js，问题就来了,因为我们vue现在开发的过程基于了es6语法,所以时严格模式,然而拟引入jquery和WebUploader自带的包之后呢,你就会发现有错误出现,这里会出现的错误时Uncaught TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them ，因为严格模式下不支持callee，但是问题出现了是不是解决不了呢？？

#### 2.根据我定位的问题发现引入webupload.js的包在4405行会出现这个错误,我们现在定位到这里这段代码有问题,修改的方法如下:
        var changeFn = (function even(that, e){
          var clone;
 
          me.files = e.target.files;
 
          // reset input
          clone = that.cloneNode( true );
          clone.value = null;
          that.parentNode.replaceChild( clone, that );
 
          input.off();
          input = $(clone).on('change', function(e){
              even(this, e);
          }).on('mouseenter mouseleave', mouseHandler);
 
          owner.trigger('change');
        });
 
        input.on('change', function(e){
          changeFn(this, e);
        });

#### 3.当我们做了这个修改之后,这时你同样的也会发现有问题,当你点击选择文件之后你会发现文件没有显示出来,文件显示出来的情况:1.你第一次选择文件没有显示,你要选择其他不同的文件这时就会出现文件；2.你如果第一次选择文件接着你还是选择相同的文件你还是不会显示文件出来,其实我们现在读读之前的那个源码发现我们时触发change事件,我们这里修改了源码但是我们这里时利用了闭包,但是并没有自调用这个函数,所以我们这里的问题就定为到了，对代码进行第二次更改:
        var changeFn = (function even(that, e){
          var clone;
 
          me.files = e.target.files;
 
          // reset input
          clone = that.cloneNode( true );
          clone.value = null;
          that.parentNode.replaceChild( clone, that );
 
          input.off();
          input = $(clone).on('change', function(e){
              even(this, e);
          }).on('mouseenter mouseleave', mouseHandler);
 
          owner.trigger('change');
        })(this,e); // 注意这里的修改,别掉了,网上有的博主,修改这里但是都没有调用就分享出去了,所以请大家注意这个点
 
        input.on('change', function(e){
          changeFn(this, e);
        });
