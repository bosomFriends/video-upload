## 视频上传
##### 最近项目开发的过程中老板需要做一个视频上传带有进度条的功,并且希望上传的进度是符合真实的情况,这样就需要前端和后端配合,以往的视频上传做的进度条都是前端这边处理这种处理是用axios请求里面onUploadProgress的progressEvent来获取已上传文件大小和别上传文件的大小来达到进度的效果,但是这种只是前端将数据传到后端的数据库,并没有传到服务器上面,这种上传的效果是不符合老板的要求,并且视频要考虑到视频的大小,如果超过了2G这种大型的文件用之前的传法也是会比较慢的,所以现在我研究一下分片的上传的原理,利用分片上传这样速度效率也会提高,并且即使可以暂停上传再次重新上传,不会影响效果,以前我们常规操作就是实现不了这种效果。废话不多说,请你们来使用一下这个demo.
