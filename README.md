项目介绍



- 1.了解真实项目开发的架构设计
  - （1）前端三层分离的编程思想
  - （2）网络请求层单独处理的编程思想



- 2.培养大家的项目思维能力
  - （1）独立小功能需求分析->页面需求分析->整个项目需求分析
  - （2）熟练运用ajax+模板引擎实现页面业务逻辑功能



- 3.提升大家的项目经验
  - （1）项目技术难点的分析与解决方案
  - （2）各种第三方插件使用



01-登录功能

1.1-登录功能业务逻辑



    <script>
        //入口函数
        $(function () {
          /* 登录功能思路
          1.给登录按钮注册点击事件
          2.阻止默认跳转事件（表单submit会自动跳转页面）
          3.获取用户名和密码
          4.非空判断
          5.ajax发送请求
          6.处理响应结果   a.成功：跳转管理系统首页    b.失败：提示用户
           */
          //1.给登录按钮注册点击事件
          $('.input_sub').click(function (e) {
            //2.阻止默认跳转事件（表单submit会自动跳转页面）
            e.preventDefault();
            //3.获取用户名和密码
            var username = $('.input_txt').val().trim();//去除前后空格
            var password = $('.input_pass').val().trim();
            //4.非空判断
            if (username == '' || password == '') {
              alert('用户名与密码不能为空');
              return;
            };
            //5.ajax发送请求
            $.ajax({
              url: 'http://localhost:8080/api/v1/admin/user/login',
              type: 'post',
              dataType: 'json',
              data: {
                username,
                password
              },
              success: function (backData) {
                console.log(backData);
                //6.处理响应结果  a.成功：跳转管理系统首页  b.失败：提示用户
                if(backData.code == 200){
                  //跳转首页
                  window.location.href = './index.html';
                }else{
                  alert(backData.msg);
                }
              }
            });  
          });
        });
      </script>

1.2-bootstrap模态弹出框使用

- 传送门：https://v3.bootcss.com/javascript/#modals





- 1.选择你想要的模态框，将HTML代码复制粘贴到你的项目HTML中（可以直接放到最后面）



- 2.弹出模态框



- 3.登录成功，模态框消失之后跳转首页



    <!-- 模态框 -->
      <div id="myModal" class="modal fade" tabindex="-1" role="dialog">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span
                  aria-hidden="true">&times;</span></button>
              <h4 class="modal-title">友情提示</h4>
            </div>
            <div class="modal-body">
              <p></p>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-primary" data-dismiss="modal">确认</button>
            </div>
          </div><!-- /.modal-content -->
        </div><!-- /.modal-dialog -->
      </div><!-- /.modal -->



02-首页模块

[拓展]live-sever插件使用

- live-server:插件作用
  - 当我们修改了页面代码的时候，页面会自动刷新
    - 以前每一次修改代码，都要重新运行页面，特别的麻烦。有了这个插件，我们修改代码后页面会自动刷新
    - 插件工作原理：搭建一个本地服务器，实时监听你的文件修改。只要修改，服务器自动刷新页面







1.1-用户信息功能

- index.html页面

    <!-- 一：查询个人信息+退出登录 -->
        <script>
            $(function () {
                /*1.查询个人信息
                1.1 页面一加载发送ajax请求
                1.2 响应数据之后渲染到页面 
                */
                //1.1 ajax请求
                $.ajax({
                    url: http://localhost:8080/api/v1/admin/user/info',
                    type: 'get',
                    dataType: 'json',
                    success: function (backData) {
                        console.log(backData);
                        //1.2 渲染页面
                       $('.user_info>img').attr('src',backData.data.userPic);
    			       $('.user_center_link>img').attr('src',backData.data.userPic);
                       $('.user_info>span').text('欢迎  ' + backData.data.nickname);
                    }
                });
            });
        </script>



1.2-token工作原理介绍

- 1.为什么要有token?
  - 默认情况下，HTTP是一个无状态协议，也就是说任何浏览器都可以访问服务器，但是服务器并不能知道浏览器到底是属于哪个用户的。



- 2.token工作流程介绍





- 3.大事件项目处理token解决方案
- (1)登录成功之后，使用localstorage将token持久化保存到浏览器本地







（2）登录之后，所有的请求都需要在请求头中添加token字段

- 原生ajax实现
  - 弊端：每一个请求都需要设置请求头，非常的麻烦

    //(1).实例化ajax对象
    var xhr = new XMLHttpRequest();
    //(2).设置请求方法和地址
    //get请求的数据直接添加在url的后面 格式是 url?key=value
    xhr.open('get', 'http://localhost:8080/api/v1/admin/user/info');
    /*发送请求之前设置特殊请求头：登录验证如果不发送，浏览器会认为你没有登录，所以不会返回给你数据
    */         xhr.setRequestHeader('Authorization','eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjoiYWRtaW4iLCJleHAiOjE1Njc5NTc3MDIsImlhdCI6MTU2Nzk1NTkwMn0.CEc7tam-M9JBKNFNoCnOK9SZ5zqEeYTZSxNyYWwHIZGbHwtp82UCG462-h8CXZfHTlKU2iFtor62G-bIbSsgH5zDa5rCgVvs6FYLV6A69Cih3lAOM2icrBeCzFlIXqt2v7TP2Z4PQyzrzr2Q8codyluIaCRjs9UK1mwxi5PFvuQ');
    //(3).发送请求
    xhr.send();
    //(4).注册回调函数
    xhr.onload = function() {
    	console.log(xhr.responseText)
    };



- jq实现方案
  - jq官方文档介绍（比较简陋）：http://jquery.cuishifeng.cn/jQuery.ajaxSetup.html
  - 菜鸟教程网站介绍（比较详细）:https://www.runoob.com/jquery/ajax-ajaxsetup.html
  - $.ajaxSetup():设置全局 AJAX 默认选项。





- 在jquery-1.12.4.min.js源码的最底部添加全局的 $.ajaxSetup配值

    /* 
    1.  $.ajaxSetup() : 为所有的AJAX请求设置默认值
    2.  beforeSend(xhr):	发送请求前运行的函数。(这个方法可以根据后台需求设置一些固定请求头)
    */
    $.ajaxSetup({
        //在发送ajax之前，给所有的请求都设置请求头（从localStorage中读取token）
        beforeSend(xhr) {
           xhr.setRequestHeader("Authorization", localStorage.getItem('token'));
        },
        error(xhr,status,error) {//请求错误的时候会进入这个方法进行拦截
            console.log(xhr.responseText);
            console.log(status);//错误状态
            console.log(error);//错误信息
            if(error == 'Forbidden'){//用户未登录
                alert('请先登录！')
                window.location = './login.html';//跳转登陆页面
            }; 
        }
    });



1.3-退出登录功能

- index.html页面

    			/*2.退出登录
                2.1 删除token
                2.2 跳转登录页
                 */
                 $('.logout').click(function(){
                    //2.1 删除token
                    localStorage.removeItem('token');
                    //2.2 跳转登录页
                    window.location.href = './login.html';
                 });



1.4-服务器接口文档封装(http.js)

- 1.目前存在的问题
  - a.所有的接口文档都是在各自的页面写死
  - b.服务器基地址与接口地址写在一起，不便于维护
- 2.解决方案
  - a.将项目所有的接口地址写在一个文件中（http.js）
    - 这样以后接口发生修改就不用修改每一个页面，直接修改这个文件即可
  - b.将http.js文件中的基地址与接口地址分开写
    - 因为服务器的基地址有可能会变。一般企业会有一个测试环境与正式发布环境的基地址
- http.js文件
  - 什么样的js文件需要使用沙箱模式：这个js文件需要在很多个页面导入。 
  - 什么样的js文件不需要使用沙箱模式：这个js文件不需要很多个地方导入。例如一个网页的前端三层中的js文件

    /* 沙箱模式 */
    (function(w){
        var baseURL = 'http://localhost:8080/api/v1'
        var BigNew = {
            baseURL:baseURL,//基地址
            user_login:      baseURL + '/admin/user/login',//用户登录
            user_info:       baseURL + '/admin/user/info',//用户信息
            user_detail:     baseURL + '/admin/user/detail',//用户详情
            user_edit:       baseURL + '/admin/user/edit',//用户编辑
            category_list:   baseURL + '/admin/category/list',//文章类别查询
            category_add:    baseURL + '/admin/category/add',//文章类别新增
            category_search: baseURL + '/admin/category/search',//文章类别搜索
            category_edit:   baseURL + '/admin/category/edit',//文章类别编辑
            category_delete: baseURL + '/admin/category/delete',//文章类别删除
            article_query:   baseURL + '/admin/article/query',//文章搜索
            article_publish: baseURL + '/admin/article/publish',//文章发布
            article_search:  baseURL + '/admin/article/search',//文章信息查询
            article_edit:    baseURL + '/admin/article/edit',//文章编辑
            article_delete:  baseURL + '/admin/article/delete',//文章删除
            comment_list:    baseURL + '/admin/comment/search',//文章评论列表
            comment_pass:    baseURL + '/admin/comment/pass',//文章评论通过
            comment_reject:  baseURL + '/admin/comment/reject',//文章评论不通过
            comment_delete:  baseURL + '/admin/comment/delete',//文章评论删除
        };
    
        //暴露接口
        w.BigNew = BigNew;
    })(window);





1.5-[知识点]iframe标签使用

- w3c传送门：https://www.w3school.com.cn/tags/tag_iframe.asp

1.5.1-iframe嵌套单页面



    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            iframe{
                width: 800px;
                height: 600px;
                display: block;
                margin: 100px auto;
            }
        </style>
    </head>
    <body>
        <!-- iframe标签作用：在一个页面中嵌套另一个页面 -->
        <iframe src="https://www.jd.com" frameborder="0"></iframe>
    </body>
    </html>

1.5.2-iframe嵌套多页面

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            iframe{
                width: 800px;
                height: 600px;
                display: block;
                margin: 100px auto;
            }
        </style>
    </head>
    <body>
        <!-- 
            a标签的target属性
                在本页面打开链接   : __self
                在新页面打开链接   : __blank
                在iframe中打开链接 : iframe标签的name属性值   
         -->
        <a target="myIframe" href="./index1.html">页面1</a>
        <a target="myIframe" href="./index2.html">页面2</a>
        <a target="myIframe" href="./index3.html">页面3</a>
        <!-- iframe标签作用：在一个页面中嵌套另一个页面 -->
        <iframe name="myIframe" src="./index1.html" frameborder="0"></iframe>
    </body>
    </html>



1.6-首页侧边栏功能



- 1.给index.html文件添加一个iframe标签

    <!-- 右侧主体内容 -->
        <div class="main" id="main_body">
            <!-- 右侧添加iframe，默认src指向首页图表页面main_count.html -->
            <iframe name="main_frame" src="main_count.html" frameborder="0" width="100%" height="100%"></iframe>
        </div>

- 2.给index.html页面左侧导航栏的a标签添加target属性，指向iframe标签的name属性值
  - 作用：让这个a标签的href属性链接在右侧iframe标签中打开

    <!-- 左侧导航栏 -->
        <div class="menu">
            <div class="level01 active"><a href="main_count.html" target="main_frame"><i class="iconfont icon-yidiandiantubiao04"></i><span>首页</span></a></div>
            <div class="level01"><a href="#"><i class="iconfont icon-icon-article"></i><span>文章管理</span><b class="iconfont icon-arrowdownl"></b></a></div>
            <ul class="level02">
                <li><a href="article_list.html" target="main_frame"><i class="iconfont icon-previewright"></i><span>文章列表</span></a></li>
                <li><a href="article_release.html" target="main_frame"><i
    class="iconfont icon-previewright"></i><span>发表文章</span></a></li>
                <li><a href="article_category.html" target="main_frame"><i
    class="iconfont icon-previewright"></i><span>文章类别管理</span></a></li>
            </ul>
            <div class="level01"><a href="comment_list.html" target="main_frame"><i class="iconfont icon-comment"></i><span>评论管理</span></a></div>
            <div class="level01" id="user"><a href="user.html" target="main_frame"><i class="iconfont icon-user"></i><span>个人中心</span></a></div>
        </div>



- 3.实现左侧导航栏点击a标签高亮效果与文章管理下拉效果
  - 技术点：原生DOM对象的click()方法与jq对象click()方法的区别
    - 原生DOM对象的click()方法 ： 触发该对象的默认行为 + 点击事件
    - jq对象的click()方法 ： 只触发该对象的点击事件

    /* 3.点击左侧导航栏效果 */
        //1.一级列表
        $('.level01').click(function () {
            //1.1 排他思想修改样式
            $(this).addClass('active').siblings().removeClass('active');
            //1.2 如果点击的是文章管理：则应该下滑二级列表ul
            if ($(this).next().hasClass('level02')) {
                //滑入滑出切换
                $(this).next().slideToggle();
                //旋转90度切换
                $(this).find('b').toggleClass('rotate0');
                //默认选中第一个 : DOM对象
                $('.level02>li>a').first()[0].click();
            } else {
                //如果点击的不是文章管理一级菜单列表，则移除二级列表的选中样式
                $('.level02>li').removeClass('active');
            }
        });
    
        //2.二级列表
        $('.level02>li').click(function () {
            $(this).addClass('active').siblings().removeClass('active');
        });



03-个人中心模块



1.1-个人中心查询与修改



- 1.细节优化：给index.html页面的右侧顶部个人中心按钮设置target属性指向iframe的name，并且注册行内事件：主动触发左侧个人中心按钮的点击事件
  - href="./user.html" + target="main_frame":在iframe中打开个人中心页面
  - onclick="$('#user').trigger('click')" ：主动触发左侧导航栏个人中心的点击事件,来修改a标签高亮效果

    <div class="user_center_link">
    	<a href="./user.html" onclick="$('#user').trigger('click')" target="main_frame">个人中心</a>
    	<img src="images/2.jpg" alt="person">
    	<a href="javascript:void(0)" class="logout"><i class="iconfont icon-tuichu"></i> 退出</a>
    </div>

- 2.页面一加载，ajax请求个人详细信息，渲染页面
  - 细节：渲染页面的表单元素类名与服务器返回的对象属性名一致，就可以写一个for-in循环优化代码

    //1.页面一加载：ajax请求个人详情信息，渲染页面
        $.ajax({
            url: BigNew.user_detail,
            type: 'get',
            dataType: 'json',
            success: function (backData) {
                console.log(backData);
                //渲染页面
                // $('input.username').val(backData.data.username);
                // $('input.nickname').val(backData.data.nickname);
                // $('input.email').val(backData.data.email);
                // $('input.password').val(backData.data.password);
                //遍历对象优化代码
                for (var key in backData.data) {
                    $('input.' + key).val(backData.data[key]);
                }
                $('img.user_pic').attr('src', backData.data.userPic);
            }
        });



- 3.文件预览功能
  - 固定的四个步骤

    //2.文件预览
        //1.给file表单元素注册onchange事件
        $('#exampleInputFile').change(function () {
            //1.2 获取用户选择的图片
            var file = this.files[0];
            //1.3 将文件转为src路径
            var url = URL.createObjectURL(file);
            //1.4 将url路径赋值给img标签的src
            $('.user_pic').attr('src', url);
        });

- 4.编辑个人信息
  - 使用formData上传带文件的参数
  - 注意点:当前页面所在的窗口window对象是user.html,如果想要获取父窗口，则需要使用window.parent，这种用法一般用于获取iframe标签的父窗口。
    - https://www.runoob.com/jsref/prop-win-parent.html

    //3.编辑个人信息(fromdata上传文件)
        $('#form').on('submit', function (e) {
            //禁用表单默认提交事件
            e.preventDefault();
            $.ajax({
                url: BigNew.user_edit,
                type: 'post',
                dataType: 'json',
                data: new FormData(this),
                contentType: false,
                processData: false,
                success: function (backData) {
                    console.log(backData);
                    if (backData.code == 200) {
                        alert('修改成功');
                        /* 
                        第一种做法: 让父窗口刷新
                        window:            当前页面窗口 user.html
                        window.parent:     当前页面父窗口 index.html
                        window.parent.location.reload();
                        */
                        //第二种解决办法:
                      	//重新发送ajax请求,获取网站管理员用户的个人信息
                      	$.ajax({
                         url: window.BigNew.user_info,
                         success: function(backData) {
                          //console.log(backData);
                           if (backData.code == 200) {
                            //给父页面的显示个人信息的标签设置新的值.
                             parent.$(".user_info>span>i").text(backData.data.nickname);
                             parent.$(".user_info>img").attr("src", backData.data.userPic);
                             parent.$(".user_center_link>img").attr("src", backData.data.userPic);
                          }
                        }
                       });
                    }
                }
            });
        });



- 完整代码
- 在user.html中导入http.js与user.js

    <script src="./libs/http.js"></script>
    <script src="./js/user.js"></script>

- user.js文件

    //入口函数
    $(function () {
        //1.页面一加载：ajax请求个人详情信息，渲染页面
        $.ajax({
            url: BigNew.user_detail,
            type: 'get',
            dataType: 'json',
            success: function (backData) {
                console.log(backData);
                //渲染页面
                // $('input.username').val(backData.data.username);
                // $('input.nickname').val(backData.data.nickname);
                // $('input.email').val(backData.data.email);
                // $('input.password').val(backData.data.password);
                //遍历对象优化代码
                for (var key in backData.data) {
                    $('input.' + key).val(backData.data[key]);
                }
                $('img.user_pic').attr('src', backData.data.userPic);
            }
        });
        //2.文件预览
        //1.给file表单元素注册onchange事件
        $('#exampleInputFile').change(function () {
            //1.2 获取用户选择的图片
            var file = this.files[0];
            //1.3 将文件转为src路径
            var url = URL.createObjectURL(file);
            //1.4 将url路径赋值给img标签的src
            $('.user_pic').attr('src', url);
        });
    
        //3.编辑个人信息(fromdata上传文件)
        $('#form').on('submit', function (e) {
            //禁用表单默认提交事件
            e.preventDefault();
            $.ajax({
                url: BigNew.user_edit,
                type: 'post',
                dataType: 'json',
                data: new FormData(this),
                contentType: false,
                processData: false,
                success: function (backData) {
                    console.log(backData);
                    if (backData.code == 200) {
                        alert('修改成功');
                        /* 
                        window:            当前页面窗口 user.html
                        window.parent:     当前页面父窗口 index.html
                        */
                        parent.window.location.reload();
                    }
                }
            });
        });
    });



1.2-[知识点]label标签for属性使用(自定义上传按钮样式)





    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <!-- 技术点：自定义上传按钮样式
            1.给label标签的for属性赋值,值是input标签的id,
        那这样的话点击label就相当于点击了input
            2.隐藏这个input，然后在label标签里面添加img标签即可
         -->
         <input style="display: none" id="aaa" type="file" value="上传文件">
         <br>
        <label for="aaa">
            <img src="./uploads_icon.jpg" style="height: 50px" alt="">
        </label>
        
    </body>
    </html>



- 课堂练习：将个人中心页面的上传按钮替换成一张图片

04-文章分类模块功能



- article_category.html页面
- 本模块技术难点

    /*技术难点 
                1.点击新增按钮与编辑按钮都要弹出模态框
                2.新增按钮的业务逻辑与编辑按钮业务逻辑不同
                    新增按钮弹出的模态框
                        (1)表单文本清空
                        (2)点击取消，清空表单文本方便下一次添加
                        (3)点击新增：ajax发送请求给服务器
                            url:/admin/category/add
                            参数： name : 分类名称  slug:分类别名
                    编辑按钮弹出的模态框
                        (1)表单文本为当前点击编辑的文字类别数据
                        (2)点击取消：清空表单文本方便下一次编辑
                        (3)点击编辑：ajax发送请求给服务器
                            url:/admin/category/edit
                            参数：name : 分类名称  slug:分类别名 id:编辑的文字id
                
                解决方案：结合bootstrap官方文档模态框使用
                1.给新增按钮与编辑按钮a标签设置行内属性：data-toggle="modal" data-target="#myModal"
                    作用：点击a标签自动弹出模态框
                2.给模态框注册事件 $('#myModal').on('show.bs.modal', function (e) {})
                    作用：弹出模态框之前会执行这个事件处理函数
                    e.relatedTarget : 获取弹出模态框的事件触发源（点击哪个a弹出来的）
                3.给模态框的取消按钮和确认按钮注册事件
                    取消按钮：隐藏模态框，并且清空表单文本： dom表单.reset()
                    确认按钮：
                        如果e.relatedTarget是新增 ->则执行新增按钮逻辑
                        如果e.relatedTarget是编辑 ->则执行编辑按钮逻辑
                */



1.1-查询所有文章分类

    //1.页面一加载发送ajax请求数据
        $.ajax({
            url: BigNew.category_list,
            type: 'get',
            dataType: 'json',
            success: function (backData) {
                console.log(backData);
                //模板引擎渲染页面 
                $('.category_table>tbody').html(template('art_cat', backData));
            }
        });

- 模板
  - 细节：分别给编辑按钮与删除按钮添加自定义属性data-id用来存储文章分类的id
    - 这样到时候点击的时候，只需要取出来这个属性值就知道点击的是哪一个了

    <script id="art_cat" type="text/html">
        {{ each data v }}
        <tr>
            <td>{{ v.name }}</td>
            <td>{{ v.slug }}</td>
            <td class="text-center">
                <a href="javascript:void(0);" data-toggle="modal" data-target="#myModal" "class=" btn btn-info btn-xs" data-id="{{ v.id }}">编辑</a>
                <a href="javascript:void(0)" class="btn btn-danger btn-xs delete" data-id="{{ v.id }}"">删除</a>
            </td>
        </tr>
        {{ /each }}
    </script>



1.2-[知识点]bootstrap模态框事件介绍

- bootstrap官网传送门：https://v3.bootcss.com/javascript/#modals







    <!DOCTYPE html>
    <html lang="zh-CN">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Bootstrap Template</title>
        <!-- 使用bootstrap需要导入三个文件 -->
        <script src="./bootstrap/js/jquery-1.12.4.min.js"></script>
        <script src="./bootstrap/js/bootstrap.min.js"></script>
        <link href="./bootstrap/css/bootstrap.min.css" rel="stylesheet">
    </head>
    
    <body>
        <!-- 模态框 -->
        <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span
                                aria-hidden="true">&times;</span></button>
                        <h4 class="modal-title" id="exampleModalLabel">新增分类</h4>
                    </div>
                    <div class="modal-body">
                        <form id="form">
                            <div class="form-group">
                                <label for="recipient-name" class="control-label">分类名称:</label>
                                <input type="text" class="form-control" id="recipient-name" name="name">
                            </div>
                            <div class="form-group">
                                <label for="message-text" class="control-label">分类别名:</label>
                                <input class="form-control" id="message-text" name="slug">
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-default cancel">取消</button>
                        <button type="button" class="btn btn-primary confirm">确认</button>
                    </div>
                </div>
            </div>
        </div>
    
        <a href="javascript:void(0);" class="btn btn-success btn-xs" id="xinzengfenlei"data-toggle="modal" data-target="#myModal">新增分类</a>
        <a href="javascript:void(0);" data-toggle="modal" data-target="#myModal" class="btn btn-info btn-xs">编辑</a>
        
    
    
        <script>
            //1.模态框出现之前
            $('#myModal').on('show.bs.modal', function (e) {
                //获取模态框事件触发源
                var target = e.relatedTarget;
                console.log(e.relatedTarget);
                if (target == $('#xinzengfenlei')[0]) {
                        //执行新增分类逻辑
                        alert('你点击了新增按钮');
                        //(1)设置按钮文本为新增
                        $('#myModal .confirm').text('新增');
                    } else {
                        //执行编辑分类逻辑
                        alert('你点击了编辑按钮');
                        //(1)设置按钮文本为编辑
                        $('#myModal .confirm').text('编辑');
                    }
            });
    
    
            //2.取消按钮点击事件
            $('#myModal .cancel').on('click', function (e) {
                    // do something...
                //隐藏模态框
                $('#myModal').modal('hide')
            });
    
            //3.确认按钮点击事件
            $('#myModal .confirm').on('click', function (e, a) {
                    // do something...
                    if ($(this).text() == '新增') {
                        alert('新增成功');
                        //刷新页面
                        window.location.reload();
                    } else {//编辑
                        alert('编辑成功');
                        //刷新页面
                        window.location.reload();
                    }
                });
    
        </script>
    </body>
    
    </html>



1.3-新增文章分类功能

- 给新增按钮添加data-toggle="modal" data-target="#myModal"

    <td colspan="3" class="text-center">
    	<a href="javascript:void(0)" class="btn btn-success" id="xinzengfenlei" data-toggle="modal" data-target="#myModal">新增分类</a>
    </td>

    //1.模态框出现之前
                $('#myModal').on('show.bs.modal', function (e) {
                    // do something...
                    //获取模态框事件触发源
                    var target = e.relatedTarget;
                    if (target == $('#xinzengfenlei')[0]) {
                        //执行新增分类逻辑
                        //(1)设置按钮文本为新增
                        $('#myModal .confirm').text('新增');
                    } else {
                        //执行编辑分类逻辑
                        
                    }
                });
    
                //2.取消按钮点击事件
                $('#myModal .cancel').on('click', function (e) {
                    // do something...
                    //清空文本框数据 ：这是DOM原生的方法
                    $('#form')[0].reset();
                    //隐藏模态框
                    $('#myModal').modal('hide')
                });
    
                //3.确认按钮点击事件
                $('#myModal .confirm').on('click', function (e, a) {
                    // do something...
                    if ($(this).text() == '新增') {
                        $.ajax({
                            url: BigNew.category_add,
                            type: 'post',
                            dataType: 'json',
                            data: {
                                name: $('#recipient-name').val(),
                                slug: $('#message-text').val(),
                            },
                            success: function (backData) {
                                if (backData.code == 201) {
                                    alert('新增成功');
                                    window.location.reload();
                                } else {
                                    alert(backData.msg);
                                };
                            }
                        });
                    } else {//编辑
                        
                    }
                });

1.4-编辑文章分类

    //1.模态框出现之前
    $('#myModal').on('show.bs.modal', function (e) {
        // do something...
        //获取模态框事件触发源
        var target = e.relatedTarget;
        if (target == $('#xinzengfenlei')[0]) {
            //执行新增分类逻辑
            //(1)设置按钮文本为新增
            $('#myModal .confirm').text('新增');
        } else {
            //执行编辑分类逻辑
            //(1)设置按钮文本为编辑
            $('#myModal .confirm').text('编辑');
            //(2)取出当前按钮所在的tr的name值赋值给模态框的表单
            $('#recipient-name').val($(target)
             .parent().prev().prev().text());
            //(3)取出当前按钮所在的tr的slug值赋值给模态框的表单
            $('#message-text').val($(target).parent().prev().text());
            //(2)将当前按钮的自定义属性data-id赋值给模态框编辑按钮的自定义属性data-id
            $('#myModal .confirm').attr('data-id', $(target)
             .attr('data-id'));
        }
    });
    
    //2.取消按钮点击事件
    $('#myModal .cancel').on('click', function (e) {
        // do something...
        //清空文本框数据 ：这是DOM原生的方法
        $('#form')[0].reset();
        //隐藏模态框
        $('#myModal').modal('hide')
    });
    
    //3.确认按钮点击事件
    $('#myModal .confirm').on('click', function (e, a) {
        // do something...
        if ($(this).text() == '新增') {
            $.ajax({
                url: BigNew.category_add,
                type: 'post',
                dataType: 'json',
                data: {
                    name: $('#recipient-name').val(),
                    slug: $('#message-text').val(),
                },
                success: function (backData) {
                    if (backData.code == 201) {
                        alert('新增成功');
                        window.location.reload();
                    } else {
                        alert(backData.msg);
                    };
                }
            });
        } else {//编辑
            $.ajax({
                url: BigNew.category_edit,
                type: 'post',
                dataType: 'json',
                data: {
                    name: $('#recipient-name').val(),
                    slug: $('#message-text').val(),
                    id: $(this).attr('data-id')
                },
                success: function (backData) {
                    if (backData.code == 200) {
                        alert('编辑成功');
                        window.location.reload();
                    } else {
                        alert(backData.msg);
                    };
                }
            });
        }
    });



1.4-删除文章分类

- 注意点：删除按钮是模板引擎动态添加的，所以需要注册委托事件

    //4.删除按钮 ：这个按钮是动态添加的，所以需要注册委托事件
    //注意点：注册委托事件的父元素不能是动态添加的，否则无法委托
    $('.category_table>tbody').on('click', '.delete', function () {
        $.ajax({
            url: BigNew.category_delete,
            type: 'post',
            dataType: 'json',
            data: {
                id: $(this).attr('data-id')
            },
            success: function (backData) {
                if (backData.code == 204) {
                    alert('删除成功');
                    window.location.reload();
                } else {
                    alert(backData.msg);
                };
            }
        });
    });



05-文章列表模块

- 该模块有三大难点
  - 1.筛选功能，select下拉框有一个所有分类,但是接口文档不支持这个参数
    - 需求： 页面展示：分类名称     接口参数：分类id
    - 解决方案：利用select优先获取value值的特点
  - 2.分页插件
    - a.如果用户点击筛选功能，则总页数发生变化。该插件需要重新加载
    - b.性能优化：只有当用户点击的页数和当前页数不一致的时候才会发送ajax请求数据
  - 3.编辑文章与发布文章页面
    - 日期选择插件
    - 富文本编辑器插件

1.1-文章列表页面



1.1.1-查询文章分类

    //入口函数
    $(function () {
        //1.页面一加载：请求分类列表渲染到下拉菜单
        $.ajax({
            url: BigNew.category_list,
            type: 'get',
            dataType: 'json',
            success: function (backData) {
                console.log(backData);
                //模板引擎渲染页面 
                $('#selCategory').html(template('category_list', backData));
            }
        });
    };

- 文章分类模板

    <!-- 下拉菜单模板 -->
    <script type="text/html" id="category_list">
        <option >所有分类</option>
        {{ each data }}
        <option >{{ $value.name }}</option>
        {{ /each }}
    </script>



1.1.2-[知识点]select下拉菜单使用

- select标签特点
  - 1.默认只会获取select中option标签的值，不会获取select自己的value值
  - 2.如果option有value属性，则select会优先获取option的value值
  - 3.如果option没有value属性，则select会获取option的text值



    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <!-- select标签特点
            1.默认只会获取select中option标签的值，不会获取select自己的value值
            2.如果option有value属性，则select会优先获取option的value值
            3.如果option没有value属性，则select会获取option的text值
    
         -->
        <select name="" id="" value="111">
            <option value="a">苹果</option>
            <option value="b">香蕉</option>
            <option value="c">中国</option>
            <option value="">前端</option>
            <option >安卓</option>
        </select>
    
        <script src="./jquery-1.12.4.min.js"></script>
        <script>
            console.log($('select').val());
        </script>
    </body>
    </html>



1.1.3-查询文章列表

    //二:一进到页面就要获取默认条件下的文章们并显示. 
    $.ajax({
        url: BigNew.article_query,
        data: {
            type: $('#selCategory').val().trim(),
            state: $('#selStatus').val().trim(),
            page: 1,
            perpage: 8
        },
        success: function (backData) {
            console.log(backData);
            if (backData.code == 200) {
                //调用模板引擎核心方法
                var resHtml = template('arti_list', backData);
                $('tbody').html(resHtml);
            }
        }
    });
    
    //三:给筛选按钮设置点击事件,获取满足条件的文章们
    $('#btnSearch').on('click', function (e) {
        e.preventDefault();
        //发送ajax请求.
        $.ajax({
            url: BigNew.article_query,
            data: {
                type: $('#selCategory').val().trim(),
                state: $('#selStatus').val().trim(),
                page: 1,
                perpage: 8
            },
            success: function (backData) {
                if (backData.code == 200) {
                    //调用模板引擎核心方法
                    var resHtml = template('arti_list', backData);
                    $('tbody').html(resHtml);
                }
            }
        });
    });
    
    //注意: 上面发送ajax请求的代码是相同的,我们完全可以把他封装成一个函数再调用



- 文章列表 模板引擎模板
  - 细节注意点
    - 点击编辑按钮：需要跳转编辑文章页面，可以使用window.location.href传参
    - 点击删除按钮：该接口需要传递文章的id，可以给删除添加添加自定义行内属性data-id存储文章id

    <!-- 文章列表模板 -->
        <script id="arti_list" type="text/html">
            {{each data.data v}}
            <tr>
                <td>{{v.title}}</td>
                <td>{{v.author}}</td>
                <td>{{v.category}}</td>
                <td class="text-center">{{v.date}}</td>
                <td class="text-center">{{v.state}}</td>
                <td class="text-center">
                    <a href="article_edit.html?id={{v.id}}" class="btn btn-default btn-xs">编辑</a>
                    <a href="javascript:void(0);" data-id="{{v.id}}" class="btn btn-danger btn-xs delete">删除</a>
                </td>
            </tr>
            {{/each}}
        </script>





1.1.4-[知识点]pagination分页插件使用

- 官网文档学习传送门：http://josecebe.github.io/twbs-pagination/
  - 这个插件是基于bootstrap开发的，所以使用这个插件的前提是导入bootstrap





- 这个插件难点：一旦总页数发生变化，需要销毁旧的插件，重新加载新插件,在文档中可以看到一个方法destroy方法可以实现,但是这个用法非常的麻烦. 







我们通过查看分析源码发现有一个使用更方便的方法:

    changeTotalPages: function(totalPages, currentPage) {
        this.options.totalPages = totalPages;
        return this.show(currentPage);
    },
    



1.1.5-完成文章列表分页查询

- 思路分析
  - 1.导包
    -     <!-- 分页插件 -->
           <script src="./libs/jquery.twbsPagination.js"></script>
  - 2.添加分页插件HTML结构
    -     <div class="row text-center">
                  <ul id="pagination" class="pagination-sm"></ul>
          </div>
    
  - 3.因为分页插件里有使用到全部数据总页数,所以在一进入到页面获取数据的回调函数里使用分页插件. 
    -     //二:一进到页面就要获取默认条件下的文章们并显示. 
          $.ajax({
              url: BigNew.article_query,
              data: {
                  type: $('#selCategory').val().trim(),
                  state: $('#selStatus').val().trim(),
                  page: 1,
                  perpage: 8
              },
              success: function (backData) {
                  console.log(backData);
                  if (backData.code == 200) {
                      //调用模板引擎核心方法
                      var resHtml = template('arti_list', backData);
                      $('tbody').html(resHtml);
          
                      //***************************
                      //分页插件
                      $('#pagination-demo').twbsPagination({
                          totalPages: backData.data.totalPage, //总页数
                          visiblePages: 7,
                          first: '首页',
                          prev: '上一页',
                          next: '下一页',
                          last: '尾页',
                          //分页插件点击页 页码数 的回调函数
                          onPageClick: function (event, page) {
                              //console.log(page); //当前点击的页数.
                              //点击 页码数 重新发送ajax请求获取文章列表
                              $.ajax({
                                  url: BigNew.article_query,
                                  data: {
                                      type: $('#selCategory').val().trim(),
                                      state: $('#selStatus').val().trim(),
                                      page: page,
                                      perpage: 8
                                  },
                                  success: function (backData) {
                                      console.log(backData);
                                      if (backData.code == 200) {
                                          //调用模板引擎核心方法
                                          var resHtml = template('arti_list', backData);
                                          $('tbody').html(resHtml);
                                      }
                                  }
                              });
          
                          }
                      });
                  }
              }
          });
  - 4.修改 筛选条件后, 筛选后的数据有可能会改变, 所以要重新加载插件.
    -     //三:给筛选按钮设置点击事件,获取满足条件的文章们
          $('#btnSearch').on('click', function (e) {
              e.preventDefault();
              //发送ajax请求.
              $.ajax({
                  url: BigNew.article_query,
                  data: {
                      type: $('#selCategory').val().trim(),
                      state: $('#selStatus').val().trim(),
                      page: 1,
                      perpage: 8
                  },
                  success: function (backData) {
                      if (backData.code == 200) {
                          //调用模板引擎核心方法
                          var resHtml = template('arti_list', backData);
                          $('tbody').html(resHtml);
          				
                          //***************************
                          //改变了筛选条件,那总页数就有可能发生了改变
                          //调用changeTotalPages 这个方法 根据新的总页数 重新生成分页结构. 
                          $('#pagination-demo').twbsPagination('changeTotalPages',
                              backData.data.totalPage, 1);
                      }
                  }
              });
          });
  

1.1.6-文章列表分页函数封装

- 目前代码存在问题： 点击筛选按钮发送ajax请求 与 一进入到页面发送ajax请求代码相同，都是ajax请求文章列表渲染到页面上.
- 解决方案 ： 封装函数解决代码冗余问题
  需要注意: 发送ajax请求成功后, 有的需要生成分页插件,有的是分页插件总页码发生了改变要重新生成总页码,所以需要写一个回调函数.把返回的数据通过回调函数传递.

    //声明一个变量mypage,表示当前点击的分页页签数字.
    var mypage = 1;
    //发送ajax请求封装成一个函数.
    function getData(mypage, callback) {
        $.ajax({
            url: BigNew.article_query,
            data: {
                type: $('#selCategory').val().trim(), //获取文章类别
                state: $('#selStatus').val().trim(), //获取文章状态(草稿/已发布)
                page: mypage, //当前的页数
                perpage: 6 //一页显示多少条
            },
            success: function (backData) {
                if (backData.code == 200) {
                    //调用模板引擎核心方法
                    var resHtml = template('arti_list', backData);
                    $('tbody').html(resHtml);
    
                    //这里会有一些操作,
                    //比如页面一进来这里有分页插件的设置; 点击筛选按钮这里有重新渲染分页插件结构的代码
                    if (backData.data.data.length != 0 && callback != null) {
                        //有数据了就应该把分页插件结构给显示
                        $('#pagination-demo').show();
                        $('#pagination-demo').next().hide();
    
                        callback(backData); //调用回调函数,把返回来的数据backData作为实参传递.
    
                    }else if (backData.data.totalPage == mypage-1 && backData.data.data.length == 0){
                        mypage -= 1;
                        //调用changeTotalPages 这个方法 根据新的总页数 重新生成分页结构. 
                        $('#pagination-demo').twbsPagination('changeTotalPages',
                                backData.data.totalPage, mypage);
                    }else if (backData.data.data.length == 0){
                        //分页插件结构给隐藏
                        $('#pagination-demo').hide();
                        $('#pagination-demo').next().show(); //提示没有数据
                    }
                }
            }
        });
    }
    
    //二:一进到页面就要获取默认条件下的文章们并显示. 
    getData(1, function (backData) {
        //分页插件
        $('#pagination-demo').twbsPagination({
            totalPages: backData.data.totalPage, //总页数
            visiblePages: 7,
            first: '首页',
            prev: '上一页',
            next: '下一页',
            last: '尾页',
            onPageClick: function (event, page) {
                //console.log(page); //当前点击的页数.
                mypage = page; //把当前点击的这一个页码给mypage赋值. 
                getData(page, null);
            }
    
        });
    });
    
    //三:给筛选按钮设置点击事件,获取满足条件的文章们
    $('#btnSearch').on('click', function (e) {
        e.preventDefault();
        //发送ajax请求.
        getData(1, function (backData) {
            //改变了筛选条件,那总页数就有可能发生了改变
            //调用changeTotalPages 这个方法 根据新的总页数 重新生成分页结构. 
            $('#pagination-demo').twbsPagination('changeTotalPages',
                backData.data.totalPage, 1);
        });
    });



1.2-完成删除文章

- articlie_list.html页面

    /*3.删除文章：
    3.1 给删除按钮注册委托事件（动态添加的元素需要注册动态事件）
    3.2 获取当前点击按钮自定义属性 data-id
    3.3 ajax发送请求
    3.4 响应之后刷新页面 
     */
    $('table>tbody').on('click','.delete',function(){
        $.ajax({
            url:BigNew.article_delete,
            type:'post',
            dataType:'json',
            data:{
                id:$(this).attr('data-id')
            },
            success: function(backData){
                if(backData.code == 204){
                    alert('删除成功');
                    window.location.reload();
                }else{
                    alert(backData.msg);
                }
            }
        });
    });

1.3-文章编辑页面



1.3.1-[知识点]jeDate日期插件使用

- 官方文档传送门：http://www.jemui.com/uidoc/jedate.html
  - jeDate插件亮点功能：基于原生js开发。不需要依赖任何其他第三方插件





    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <!-- 导包 -->
        <link type="text/css" rel="stylesheet" href="./jedate-6.5.0/test/jeDate-test.css">
        <link rel="stylesheet" href="./jedate-6.5.0/skin/jedate.css">
        <script src="./jedate-6.5.0/src/jedate.js"></script>
    </head>
    
    <body>
        <div class="jeitem">
            <label class="jelabel">发布时间</label>
            <div class="jeinpbox">
                <input type="text" class="jeinput" id="testico" placeholder="YYYY-MM-DD">
                <div class="icons jebtns" onclick="jeDate('#testico',{trigger:false,format: 'YYYY-MM-DD',theme: { bgcolor: '#00A680', pnColor: '#00DDAA' }})"></div>
            </div>
        </div>
        <button id="btn">获取时间</button>
        <script>
    
            //加载日期插件
            jeDate('#testico', {
                trigger: 'click',
                theme: { bgcolor: "#00A680", pnColor: "#00DDAA" },//绿色主题
                format: "YYYY-MM-DD",
                isinitVal: true,
            });
    
            document.querySelector('#btn').onclick = function(){
                console.log(document.querySelector('#testico').value);
            };
        </script>
    </body>
    </html>



1.3.2-[知识点]wangEditor富文本编辑器插件使用

- wangEditor官方传送门：http://www.wangeditor.com/





- wangEditor编辑器使用也非常的方便,使用代码如下:

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>wangEditor demo</title>
    </head>
    <body>
        <div id="editor">
            <p>欢迎使用 <b>wangEditor</b> 富文本编辑器</p>
        </div>
    
        <!-- 注意， 只需要引用 JS，无需引用任何 CSS ！！！-->
        <script type="text/javascript" src="/wangEditor.min.js"></script>
        <script type="text/javascript">
            var E = window.wangEditor
            var editor = new E('#editor')
            // 或者 var editor = new E( document.getElementById('editor') )
            editor.create()
        </script>
    </body>
    </html>
       





1.3.3-获取从article_list.html页面传递过来的参数文章id

    // 入口函数
    $(function () {
        //1.获取artile_list页面传递过来的文章id
        var id = window.location.href.split('=')[1];
        console.log(id);
    };



1.3.4-加载所有文章分类

    /*2.加载文章类别 */
    $.ajax({
        type: 'get',
        url: BigNew.category_list,
        success: function (backData) {
            if (backData.code == 200) {
                //模板引擎渲染数据
                $('select.category').html(template('cat_list', backData));
            }
        }
    });

- 文章类别模板

    <!-- 准备一个 文章分类 模板 -->
    <script id="cat_list" type="text/html">
        {{each data v}}
        <option value='{{v.id}}'>{{v.name}}</option>
        {{/each}}
    </script>



1.3.5-加载文章详细信息并渲染页面

    //一:编辑逻辑第一大步
    //1.获取点击编辑按钮 跟随url传递过来的文章id
    var articleId = window.location.search.split('=')[1];
    //2.根据文章id发送ajax请求获取该文章的详细信息
    setTimeout(function () {
        $.ajax({
            url: BigNew.article_search,
            data: {
                id: articleId
            },
            success: function (backData) {
                //console.log(backData);
                if (backData.code == 200) {
                    //3.把对应的详细信息显示在对应的标签上. 
                    $('#inputTitle').val(backData.data.title); //文章标题   
                    $('.article_cover').attr('src', backData.data.cover); //文章预览图
                    $('select.category').val(backData.data.categoryId); //文章类别
                    $('#testico').val(backData.data.date); //文章发布时间
                    editor.txt.html(backData.data.content); //文章内容
                }
            }
        });
    }, 0);



1.3.6-文件预览功能实现

    /*4.文件预览 */
    //1.给file表单元素注册onchange事件
    $('#inputCover').change(function () {
        //1.2 获取用户选择的图片
        var file = this.files[0];
        //1.3 将文件转为src路径
        var url = URL.createObjectURL(file);
        //1.4 将url路径赋值给img标签的src
        $('.article_cover').attr('src', url);
    });



1.3.7-编辑文章

- 细节注意点:
  - (1)编辑文章接口文档参数很多，FromData只能自动获取表单中部分参数
    - 解决方案：使用 FromData的append()方法手动添加参数
  - (2)由于编辑按钮和草稿按钮业务逻辑相同，可以封装一个函数
    - 解决方案：只是state参数不同，，将state作为参数传递

     /*5.编辑文章 
        1.细节：编辑文章接口文档参数很多，FromData只能自动获取表单中部分参数
            解决方案：使用 FromData的append()方法手动添加参数
        2.由于编辑按钮和草稿按钮业务逻辑相同，可以封装一个函数
            解决方案：只是state参数不同，，将state作为参数传递
        */
        /* 发布 */
        $('.btn-edit').click(function (e) {
            //3.1 禁用表单默认提交事件
            e.preventDefault();
            //3.2 ajax发送请求
            editArticlie('已发布');
        });
    
        /* 存为草稿 */
        $('.btn-draft').click(function (e) {
            //3.1 禁用表单默认提交事件
            e.preventDefault();
            //3.2 ajax发送请求
            editArticlie('草稿');
        });
    
        //编辑文章ajax请求封装
        function editArticlie(state) {
            //(3)创建fd对象
            var fd = new FormData($('#form')[0]);
            //追加一个修改后的内容
            fd.append('content', tinyMCE.activeEditor.getContent());
            //追加文章id到fd中去.
            fd.append('id', id);
            //追加文章状态
            fd.append('state', state);
            //3.发送ajax请求,完成修改
            $.ajax({
                url: BigNew.article_edit,
                type: 'post',
                data: fd,
                contentType: false,
                processData: false,
                success: function (backData) {
                    if (backData.code == 200) {
                        alert('修改成功!');
                        //回退上一页
                        window.history.back();
                    } else {
                        alert(backData.msg);
                    }
                }
            });
        }

1.3.8-文章编辑页面完整代码



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>文章列表</title>
        <link rel="stylesheet" href="./libs/bootstrap/css/bootstrap.min.css">
        <link rel="stylesheet" href="css/reset.css">
        <link rel="stylesheet" href="css/iconfont.css">
        <link rel="stylesheet" href="css/main.css">
        <!-- jq -->
        <script src="./libs/jquery-1.12.4.min.js"></script>
        <!-- 引入日期插件的文件 -->
        <link rel="stylesheet" href="./libs/jedate/css/jeDate-test.css">
        <link rel="stylesheet" href="./libs/jedate/css/jedate.css">
        <script src="./libs/jedate/js/jedate.js"></script>
        <!-- 引入富文本插件的文件 -->
        <script src="./libs/wangEditor.js"></script>
        <!-- 模板引擎 -->
        <script src="./libs/template-web.js"></script>
    </head>
    
    <body>
        <div class="container-fluid">
            <div class="common_title">
                文章编辑
            </div>
            <div class="container-fluid common_con">
                <form class="form-horizontal article_form" id="form">
                    <div class="form-group">
                        <label for="inputTitle" class="col-sm-2 control-label">文章标题：</label>
                        <div class="col-sm-8">
                            <input type="text" name="title" class="form-control title" id="inputTitle" value="文章标题文字">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputCover" class="col-sm-2 control-label">文章封面：</label>
                        <div class="col-sm-10">
                            <img src="images/pic06.jpg" class="article_cover">
                            <input name="cover" type="file" id="inputCover">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputCategory" class="col-sm-2 control-label">文章类别：</label>
                        <div class="col-sm-3">
                            <select class="form-control category" name="categoryId">
                                <option>类别一</option>
                                <option>类别二</option>
                                <option>类别三</option>
                                <option selected>类别四</option>
                                <option>类别五</option>
                            </select>
                        </div>
                    </div>
                    <div class="form-group">
                        <label class="col-sm-2 control-label">发布时间：</label>
                        <!-- 日期插件结构 -->
                        <div class="col-sm-4 jeinpbox">
                            <input type="text" name="date" class="jeinput" id="testico" placeholder="YYYY-MM-DD">
                            <div class="icons jebtns"
                                onclick="jeDate('#testico',{zIndex:20999,trigger:false,format: 'YYYY-MM-DD'})">
                            </div>
                        </div>
    
                    </div>
                    <div class="form-group">
                        <label for="inputEmail3" class="col-sm-2 control-label">文章内容：</label>
                        <!-- 富文本编辑器结构 -->
                        <div class="col-sm-8" id="editor">
                            <p>传智播客 <b>黑马程序员</b> 美女田连月薪过万,人好看钱又多.</p>
                        </div>
                    </div>
                    <div class="form-group">
                        <div class="col-sm-offset-2 col-sm-10">
                            <button type="submit" class="btn btn-success btn-edit">修改</button>
                            <button type="submit" class="btn btn-default btn-draft">存为草稿</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    
        <!-- 文章类别的模板 -->
        <script id="art_cate_temp" type="text/html">
            {{each data v}}
            <option value="{{v.id}}">{{v.name}}</option>
            {{/each}}
        </script>
    
        <script src="./libs/http.js"></script>
        <script>
        //入口函数
            $(function () {
            //一:编辑页准备工作:
            //1.图片预览
            $('#inputCover').on('change', function () {
                var fileIcon = this.files[0];
                var url = URL.createObjectURL(fileIcon);
                $(this).prev().attr('src', url);
            });
    
            //2.获取所有的文章类别
            $.ajax({
                type: "get",
                url: BigNew.category_list,
                success: function (backData) {
                    //console.log(backData);
                    if (backData.code == 200) {
                        //2.获取到所有的文章类别信息后,通过模板引擎渲染到页面上
                        var resHtml = template("art_cate_temp", backData);
                        $("select.category").html(resHtml);
                    }
                }
            });
    
            //3.编辑页使用日期插件 jeDate
            jeDate("#testico", {
                zIndex: 20999,
                format: "YYYY-MM-DD",
                isTime: false,
                minDate: "2014-09-19 00:00:00"
            })
    
            //4.编辑页使用富文本编辑器  wangEditor
            var E = window.wangEditor;
            var editor = new E('#editor');
            // 或者 var editor = new E( document.getElementById('editor') )
            editor.create()
    
            //-----------------------------------------------------
            //一:编辑逻辑第一大步
            //1.获取点击编辑按钮 跟随url传递过来的文章id
            var articleId = window.location.search.split('=')[1];
            //2.根据文章id发送ajax请求获取该文章的详细信息
            setTimeout(function () {
                $.ajax({
                    url: BigNew.article_search,
                    data: {
                        id: articleId
                    },
                    success: function (backData) {
                        //console.log(backData);
                        if (backData.code == 200) {
                            //3.把对应的详细信息显示在对应的标签上. 
                            $('#inputTitle').val(backData.data.title); //文章标题   
                            $('.article_cover').attr('src', backData.data.cover); //文章预览图
                            $('select.category').val(backData.data.categoryId); //文章类别
                            $('#testico').val(backData.data.date); //文章发布时间
                            editor.txt.html(backData.data.content); //文章内容
                        }
                    }
                });
            }, 0);
    
    
            //二:编辑逻辑第二大步. 
            //给修改按钮设置点击事件. 
            $('.btn-edit').on('click',function(e){
                e.preventDefault();
                //创建fd对象
                var fd = new FormData($('form')[0]);
                //检查下form表单中要获取值的标签有么有name属性,并且name属性的值是不是和接口参数一致.
                //追加富文本编辑器 里面 修改后的文章内容  到fd对象中
                fd.append('content',editor.txt.html());
                //追加一个state键对应的值已发布到fd对象中
                fd.append('state','已发布');
                // 追加要编辑的文章的id到fd对象中
                fd.append('id',articleId);
                //发送ajax请求
                $.ajax({
                    type:'post',
                    url:BigNew.article_edit,
                    data:fd,
                    contentType:false,
                    processData:false,
                    success:function(backData){
                        console.log(backData);
                        if(backData.code == 200){
                            alert('修改成功');
                            window.history.back();//回退回去.
                        }
                    }
                });
            });
    
    
            //存草稿
            $('.btn-draft').on('click',function(e){
                e.preventDefault();
                //创建fd对象
                var fd = new FormData($('form')[0]);
                //检查下form表单中要获取值的标签有么有name属性,并且name属性的值是不是和接口参数一致.
                //追加富文本编辑器 里面 修改后的文章内容  到fd对象中
                fd.append('content',editor.txt.html());
                //state参数不写,默认为空,那就是存草稿
    
                // 追加要编辑的文章的id到fd对象中
                fd.append('id',articleId);
                //发送ajax请求
                $.ajax({
                    type:'post',
                    url:BigNew.article_edit,
                    data:fd,
                    contentType:false,
                    processData:false,
                    success:function(backData){
                        console.log(backData);
                        if(backData.code == 200){
                            alert('存为草稿成功');
                            window.history.back();//回退回去.
                        }
                    }
                });
            });
        	});
        </script>
    </body>
    
    </html>



1.4-文章发布页面

- 文章发布页面与文字编辑页面业务逻辑基本相同，不同点如下
  - 1.文字发布页面，不需要展示文章详情
  - 2.文章发布页面的接口url路径不同
  - 3.文章发布页面的参数比文章编辑要少（不用传文章id）

1.4.1-点击文章列表article_list.html页面的发布文章按钮，设置左侧导航栏发布文章高亮

- article_list.htm页面添加发布文章按钮点击事件
  - 技术点：$() jq选择器的第二个参数其实是document文档，一般不写默认就是当前页面的document。如果希望找到父window的元素，则可以添加第二个参数
    - 官方文档传送门：http://jquery.cuishifeng.cn/jQuery_selector_context.html



    /*4.发表文章
    设置父窗口 发表文章高亮
     */
     $('#release_btn').click(function(){
        //$() :第一个参数：选择器  第二个参数：document，默认是当前窗口document
      $('.level02>li:eq(1)',window.parent.document).addClass('active')
      .siblings()
      .removeClass('active');
    });



1.4.2-发布文章业务逻辑

    //入口函数
    $(function () {
        //一:编辑页准备工作:
        //1.图片预览
        $('#inputCover').on('change', function () {
            var fileIcon = this.files[0];
            var url = URL.createObjectURL(fileIcon);
            $(this).prev().attr('src', url);
        });
    
        //2.获取所有的文章类别
        $.ajax({
            type: "get",
            url: BigNew.category_list,
            success: function (backData) {
                //console.log(backData);
                if (backData.code == 200) {
                    //2.获取到所有的文章类别信息后,通过模板引擎渲染到页面上
                    var resHtml = template("art_cate_temp", backData);
                    $("select.category").html(resHtml);
                }
            }
        });
    
        //3.编辑页使用日期插件 jeDate
        jeDate("#testico", {
            zIndex: 20999,
            isinitVal: true,
            format: "YYYY-MM-DD",
            isTime: false,
            minDate: "2014-09-19 00:00:00"
        })
    
        //4.编辑页使用富文本编辑器  wangEditor
        var E = window.wangEditor;
        var editor = new E('#editor');
        // 或者 var editor = new E( document.getElementById('editor') )
        editor.create()
    
    
        //------------------------------------------------------------------
        //发布文章
        $('.btn-release').on('click', function (e) {
            e.preventDefault();
            //创建fd对象
            var fd = new FormData($('form')[0]);
            //检查下form表单中要获取值的标签有么有name属性,并且name属性的值是不是和接口参数一致.
            //追加富文本编辑器 里面 修改后的文章内容  到fd对象中
            fd.append('content', editor.txt.html());
            //追加一个state键对应的值已发布到fd对象中
            fd.append('state', '已发布');
            
            //发送ajax请求
            $.ajax({
                type: 'post',
                url: BigNew.article_publish,
                data: fd,
                contentType: false,
                processData: false,
                success: function (backData) {
                    console.log(backData);
                    if (backData.code == 200) {
                        alert('发布成功');
                        window.history.back(); //回退回去.
                    }
                }
            });
        })
    
    
        //发布文章存为草稿
        $('.btn-draft').on('click',function(e){
            e.preventDefault();
            //创建fd对象
            var fd = new FormData($('form')[0]);
            //检查下form表单中要获取值的标签有么有name属性,并且name属性的值是不是和接口参数一致.
            //追加富文本编辑器 里面 修改后的文章内容  到fd对象中
            fd.append('content', editor.txt.html());
            
            //发送ajax请求
            $.ajax({
                type: 'post',
                url: BigNew.article_publish,
                data: fd,
                contentType: false,
                processData: false,
                success: function (backData) {
                    console.log(backData);
                    if (backData.code == 200) {
                        alert('存为草稿成功');
                        window.history.back(); //回退回去.
                    }
                }
            });
        })
    })



1.4.3-发布文章页面html代码

    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>文章列表</title>
        <link rel="stylesheet" href="./libs/bootstrap/css/bootstrap.min.css">
        <link rel="stylesheet" href="css/reset.css">
        <link rel="stylesheet" href="css/iconfont.css">
        <link rel="stylesheet" href="css/main.css">
        <!-- jq -->
        <script src="./libs/jquery-1.12.4.min.js"></script>
        <!-- 引入日期插件的文件 -->
        <link rel="stylesheet" href="./libs/jedate/css/jeDate-test.css">
        <link rel="stylesheet" href="./libs/jedate/css/jedate.css">
        <script src="./libs/jedate/js/jedate.js"></script>
        <!-- 引入富文本插件的文件 -->
        <script src="./libs/wangEditor.js"></script>
        <!-- 模板引擎 -->
        <script src="./libs/template-web.js"></script>
    </head>
    
    <body>
        <div class="container-fluid">
            <div class="common_title">
                发表文章
            </div>
            <div class="container-fluid common_con">
                <form class="form-horizontal article_form" id="form">
                    <div class="form-group">
                        <label for="inputTitle" class="col-sm-2 control-label">文章标题：</label>
                        <div class="col-sm-8">
                            <input type="text" name="title" class="form-control title" id="inputTitle" value="文章标题文字">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputCover" class="col-sm-2 control-label">文章封面：</label>
                        <div class="col-sm-10">
                            <img src="images/pic06.jpg" class="article_cover">
                            <input name="cover" type="file" id="inputCover">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputCategory" class="col-sm-2 control-label">文章类别：</label>
                        <div class="col-sm-4">
                            <select class="form-control category" name="categoryId">
                                <option>类别一</option>
                                <option>类别二</option>
                                <option>类别三</option>
                                <option selected>类别四</option>
                                <option>类别五</option>
                            </select>
                        </div>
                    </div>
                    <div class="form-group">
                        <label class="col-sm-2 control-label">发布时间：</label>
                        <!-- 日期插件结构 -->
                        <div class="col-sm-4 jeinpbox">
                            <input type="text" name="date" class="jeinput" id="testico" placeholder="YYYY-MM-DD">
                            <div class="icons jebtns"
                                onclick="jeDate('#testico',{zIndex:20999,isinitVal:true,trigger:false,format: 'YYYY-MM-DD'})">
                            </div>
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputEmail3" class="col-sm-2 control-label">文章内容：</label>
                        <!-- 富文本编辑器结构 -->
                        <div class="col-sm-8" id="editor">
                            <p>传智播客 <b>黑马程序员</b> 美女田连月薪过万,人好看钱又多.</p>
                        </div>
                    </div>
                    <div class="form-group">
                        <div class="col-sm-offset-2 col-sm-10">
                            <button type="submit" class="btn btn-success btn-release">发布</button>
                            <button type="submit" class="btn btn-default btn-draft">存为草稿</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    
        <!-- 文章类别的模板 -->
        <script id="art_cate_temp" type="text/html">
            {{each data v}}
            <option value="{{v.id}}">{{v.name}}</option>
            {{/each}}
        </script>
    
        <script src="./libs/http.js"></script>
        <script src="./js/article_release.js"></script>
    </body>
    
    </html>



06-首页图表制作



1.1-eCharts快速入门

- eCharts官方文档传送门：https://www.echartsjs.com/zh/index.html
  - eCharts是前端开发中一个比较复杂的插件，但同时也是最容易学习的一个插件(它的官方文档写的非常的好)









1.1.1-eCharts所有的图表制作固定为三个步骤

- 1.导包
  -     <!-- 1.引入 ECharts 文件 -->
        <script src="echarts.js"></script>
- 2.HTML结构：一行代码
  -     <!-- 2.为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 600px;height:400px;"></div>
- 3.js初始化
  - 所有的图表使用步骤都是固定的，不同的图表仅仅只是配置项和数据不同而已
  -     // 3.1 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(document.getElementById('main'));
        
        // 3.2 指定图表的配置项和数据
        //所有的图表使用步骤都是固定的，不同的图表仅仅只是配置项和数据不同而已
        var option = {
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {
                data: ['销量']
            },
            xAxis: {
                data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        };
        
        // 3.3使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <!-- 1.引入 ECharts 文件 -->
        <script src="echarts.js"></script>
    </head>
    
    <body>
        <!-- 2.为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 600px;height:400px;"></div>
        <script type="text/javascript">
    
            // 3.1 基于准备好的dom，初始化echarts实例
            var myChart = echarts.init(document.getElementById('main'));
    
            // 3.2 指定图表的配置项和数据
            var option = {
                title: {
                    text: 'ECharts 入门示例'
                },
                tooltip: {},
                legend: {
                    data: ['销量']
                },
                xAxis: {
                    data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
                },
                yAxis: {},
                series: [{
                    name: '销量',
                    type: 'bar',
                    data: [5, 20, 36, 10, 10, 20]
                }]
            };
    
            // 3.3使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>
    
    </html>



1.1.2.eCharts各种酷炫图表制作只需要两个步骤

- 对比法
  - (1)通过删减代码观察图表变化 （得知某个选项的作用）
  - (2)对比其他图表，将其他图表代码复制粘贴（某个功能其他图表有，自己的图表没有）
  - (3)对比其他图表，其他图表没有的代码，我也删除（观察某个属性的意思）



- 1.点击首页顶部的案例页面，里面有各种酷炫的图表



- 2.修改配置项，根据需求定制你的图表







1.2-eCharts折线图制作



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="./echarts.js"></script>
    </head>
    
    <body>
        <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 1000px;height:350px;"></div>
        <script>
            //(1).实例化ajax对象
            var xhr = new XMLHttpRequest();
            //(2).设置请求方法和地址
            //get请求的数据直接添加在url的后面 格式是 url?key=value
            xhr.open('get', 'http://localhost:8080/api/v1/admin/data/article');
            xhr.setRequestHeader('Authorization','eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjoiYWRtaW4iLCJleHAiOjIxNzMyNzU3NjAsImlhdCI6MTU2ODQ3NTc2MH0.tAxNuBtkuB106GoBqOHUTIhC6aWx7GF1LkIWeyX0UJT7BbCDdJ7pBeTFyueu_9w94ynujO83bQz3Bo_9K5R9WTuviz1Nk3DlfoTS_9BZGfP0fd9rOCWCdCvuR9NzmVrfQahusYzgpQeb_romuhDaqi9w-II016yUFn2NIsD5HcU');
            //(3).发送请求
            xhr.send();
            //(4).注册回调函数
            xhr.onload = function () {
                var obj = JSON.parse(xhr.responseText);
                loadEchars(obj);
            };
    
            function loadEchars(obj) {
                // 基于准备好的dom，初始化echarts实例
                var myChart = echarts.init(document.getElementById('main'));
    
                var data = [];
                var date = [];
                for (var i = 0; i < obj.date.length; i++) {
                    data.push(obj.date[i].count);
                    date.push(obj.date[i].date);
                }
    
                option = {
                    tooltip: {
                        trigger: 'axis',
                        position: function (pt) {
                            return [pt[0], '10%'];
                        }
                    },
                    title: {
                        left: 'center',
                        text: '月新增文章数',
                    },
    
                    xAxis: {
                        name:'日',
                        type: 'category',
                        boundaryGap: false,
                        data: date
                    },
                    legend: {
                        data: ['新增文章'],
                        top: '40'
                    },
                    toolbox: {
                        show: true,
                        feature: {
                            dataZoom: {
                                yAxisIndex: 'none'
                            },
                            dataView: { readOnly: false },
                            magicType: { type: ['line', 'bar'] },
                            restore: {},
                            saveAsImage: {}
                        },
                        right:50
                    },
                    yAxis: {
                        type: 'value',
                        boundaryGap: [0, '100%']
                    },
                    series: [
                        {
                            name: '新增文章',
                            type: 'line',
                            smooth: true,
                            // symbol: 'none',
                            sampling: 'average',
                            itemStyle: {
                                color: '#f80'
                            },
                            areaStyle: {
                                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                                    offset: 0,
                                    color: 'rgba(255,136,0,0.39)'
                                }, {
                                    offset: .34,
                                    color: 'rgba(255,180,0,0.25)'
                                },
                                {
                                    offset: 1,
                                    color: 'rgba(255,222,0,0.00)'
                                }])
                            },
                            data: data
                        }
                    ],
                };
    
    
                // 使用刚指定的配置项和数据显示图表。
                myChart.setOption(option);
            }
        </script>
    </body>
    
    </html>



1.3-eCharts环形图制作

- 1.在官方文档找到符合需求条件的环形图



- 2.修改配置项，根据需求实现环形图



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="./echarts.js"></script>
    </head>
    
    <body>
        <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 800px;height:800px;"></div>
        <script>
            // 基于准备好的dom，初始化echarts实例
            var myChart = echarts.init(document.getElementById('main'));
    
            option = {
                title: {
                    left: 'center',
                    text: '分类文章数量比',
                },
                tooltip: {
                    trigger: 'item',
                    formatter: "{a} <br/>{b}: {c} ({d}%)"
                },
                legend: {
                    orient: 'horizontal',
                    x: 'center',
                    data: ['爱生活', '趣美味', '爱旅行', '爱电影', '爱保健'],
                    top: 30
                },
                color: ['#5885e8', '#13cfd5', '#00ce68', '#ff9565', '#20ff19'],
                series: [
                    {
                        name: '分类名称',
                        type: 'pie',
                        radius: ['50%', '70%'],
                        avoidLabelOverlap: false,
                        label: {
                            emphasis: {
                                show: true,
                                textStyle: {
                                    fontSize: '30',
                                    fontWeight: 'bold'
                                }
                            }
                        },
                        data: [
                            { value: 335, name: '爱生活' },
                            { value: 310, name: '趣美味' },
                            { value: 234, name: '爱旅行' },
                            { value: 135, name: '爱电影' },
                            { value: 548, name: '爱保健' }
                        ]
                    }
                ]
            };
    
    
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
    
        </script>
    </body>
    
    </html>



1.4-eCharts柱状图制作

- 1.在官方文档找到符合需求条件的柱状图





- 2.修改配置项，根据需求实现柱状图



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="./echarts.js"></script>
    </head>
    
    <body>
        <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 1400px;height:400px;"></div>
        <script>
            // 基于准备好的dom，初始化echarts实例
            var myChart = echarts.init(document.getElementById('main'));
    
            option = {
                title: {
                    left: 'center',
                    text: '分类访问量',
                },
                tooltip: {
                    trigger: 'axis',
                    axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                        type: 'line'        // 默认为直线，可选为：'line' | 'shadow'
                    }
                },
                legend: {
                    data: ['爱生活', '趣美味', '爱旅行', '爱电影', '爱保健'],
                    top:30
                },
                grid: {
                    left: '3%',
                    right: '4%',
                    bottom: '3%',
                    containLabel: true
                },
                xAxis: [
                    {
                        type: 'category',
                        data: ['一月', '二月', '三月', '四月']
                    }
                ],
                yAxis: [
                    {
                        type: 'value'
                    }
                ],
                color: ['#5885e8', '#13cfd5', '#00ce68', '#ff9565', '#20ff19'],
                series: [
                    {
                        name: '爱生活',
                        type: 'bar',
                        data: [320, 332, 301, 334]
                    },
                    {
                        name: '趣美味',
                        type: 'bar',
                        data: [220, 132, 101, 134]
                    },
                    {
                        name: '爱旅行',
                        type: 'bar',
                        data: [220, 182, 191, 234]
                    },
                    {
                        name: '爱电影',
                        type: 'bar',
                        data: [150, 232, 201, 154]
                    },
                    {
                        name: '爱保健',
                        type: 'bar',
                        data: [262, 118, 364, 426],
                    },
    
                ]
            };
    
    
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
    
        </script>
    </body>
    
    </html>



1.5-[补充说明]eCharts自定义配置项介绍

- 1.刚开始接触eCharts的时候，很容易被各种复杂的配置项绕晕，增加学习成本
  - a.利用上一小节通过删减配置项观察右边图表的变化。可以最快的掌握常用配置项的作用
  - b.在官网中，虽然有超级多的配置项。但是eCharts的文档写的非常的好，学习起来不会觉得有太大困难





1.6-echarts实战：完成大事件首页图表制作





    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>图表统计</title>
        <link rel="stylesheet" href="./libs/bootstrap/css/bootstrap.min.css">
        <link rel="stylesheet" href="css/reset.css">
        <link rel="stylesheet" href="css/iconfont.css">
        <link rel="stylesheet" href="css/main.css">
        <script src="./libs/jquery-1.12.4.min.js"></script>
        <script type="text/javascript" src="./libs/echarts.js"></script>
    </head>
    
    <body>
        <div class="container-fluid">
            <div class="row spannel_list">
                <div class="col-sm-3 col-xs-6">
                    <div class="spannel">
                        <em>10015</em><span>篇</span>
                        <b>总文章数</b>
                    </div>
                </div>
                <div class="col-sm-3 col-xs-6">
                    <div class="spannel scolor01">
                        <em>123</em><span>篇</span>
                        <b>日新增文章数</b>
                    </div>
                </div>
                <div class="col-sm-3 col-xs-6">
                    <div class="spannel scolor02">
                        <em>35</em><span>条</span>
                        <b>评论总数</b>
                    </div>
                </div>
                <div class="col-sm-3 col-xs-6">
                    <div class="spannel scolor03">
                        <em>123</em><span>条</span>
                        <b>日新增评论数</b>
                    </div>
                </div>
            </div>
        </div>
    
        <div class="container-fluid">
            <div class="row curve-pie">
                <div class="col-lg-8 col-md-8">
                    <div class="gragh_pannel" id="curve_show"></div>
                </div>
                <div class="col-lg-4 col-md-4">
                    <div class="gragh_pannel" id="pie_show"></div>
                </div>
            </div>
        </div>
    
        <div class="container-fluid">
            <div class="column_pannel" id="column_show"></div>
        </div>
    
    
        <!-- 折线图 -->
        <script>
            var json = '{"code":200,"msg":"获取成功","date":[{"date":"2019-05-20","count":23},{"date":"2019-05-21","count":19},{"date":"2019-05-22","count":29},{"date":"2019-05-23","count":24},{"date":"2019-05-24","count":28},{"date":"2019-05-25","count":28},{"date":"2019-05-26","count":19},{"date":"2019-05-27","count":25},{"date":"2019-05-28","count":25}]}';
            var obj = JSON.parse(json);
            loadEchars(obj);
    
            function loadEchars(obj) {
                // 基于准备好的dom，初始化echarts实例
                var myChart = echarts.init(document.getElementById('curve_show'));
    
                var data = [];
                var date = [];
                for (var i = 0; i < obj.date.length; i++) {
                    data.push(obj.date[i].count);
                    date.push(obj.date[i].date);
                }
    
                option = {
                    tooltip: {
                        trigger: 'axis',
                        position: function (pt) {
                            return [pt[0], '10%'];
                        }
                    },
                    title: {
                        left: 'center',
                        text: '月新增文章数',
                    },
    
                    xAxis: {
                        name: '日',
                        type: 'category',
                        boundaryGap: false,
                        data: date
                    },
                    legend: {
                        data: ['新增文章'],
                        top: '40'
                    },
                    toolbox: {
                        show: true,
                        feature: {
                            dataZoom: {
                                yAxisIndex: 'none'
                            },
                            dataView: { readOnly: false },
                            magicType: { type: ['line', 'bar'] },
                            restore: {},
                            saveAsImage: {}
                        },
                        right: 50
                    },
                    yAxis: {
                        type: 'value',
                        boundaryGap: [0, '100%']
                    },
                    series: [
                        {
                            name: '新增文章',
                            type: 'line',
                            smooth: true,
                            // symbol: 'none',
                            sampling: 'average',
                            itemStyle: {
                                color: '#f80'
                            },
                            areaStyle: {
                                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                                    offset: 0,
                                    color: 'rgba(255,136,0,0.39)'
                                }, {
                                    offset: .34,
                                    color: 'rgba(255,180,0,0.25)'
                                },
                                {
                                    offset: 1,
                                    color: 'rgba(255,222,0,0.00)'
                                }])
                            },
                            data: data
                        }
                    ],
                }
                // 使用刚指定的配置项和数据显示图表。
                myChart.setOption(option);
            }
        </script>
    
        <!-- 环形图 -->
        <script>
            // 基于准备好的dom，初始化echarts实例
            var myChart1 = echarts.init(document.getElementById('pie_show'));
    
            option1 = {
                title: {
                    left: 'center',
                    text: '分类文章数量比',
                },
                tooltip: {
                    trigger: 'item',
                    formatter: "{a} <br/>{b}: {c} ({d}%)"
                },
                legend: {
                    orient: 'horizontal',
                    x: 'center',
                    data: ['爱生活', '趣美味', '爱旅行', '爱电影', '爱保健'],
                    top: 30
                },
                color: ['#5885e8', '#13cfd5', '#00ce68', '#ff9565', '#20ff19'],
                series: [
                    {
                        name: '分类名称',
                        type: 'pie',
                        radius: ['50%', '70%'],
                        avoidLabelOverlap: false,
                        label: {
                            emphasis: {
                                show: true,
                                textStyle: {
                                    fontSize: '30',
                                    fontWeight: 'bold'
                                }
                            }
                        },
                        data: [
                            { value: 335, name: '爱生活' },
                            { value: 310, name: '趣美味' },
                            { value: 234, name: '爱旅行' },
                            { value: 135, name: '爱电影' },
                            { value: 548, name: '爱保健' }
                        ]
                    }
                ]
            };
            // 使用刚指定的配置项和数据显示图表。
            myChart1.setOption(option1);
        </script>
        <!-- 柱状图 -->
        <script>
            // 基于准备好的dom，初始化echarts实例
            var myChart2 = echarts.init(document.getElementById('column_show'));
    
            option2 = {
                title: {
                    left: 'center',
                    text: '分类访问量',
                },
                tooltip: {
                    trigger: 'axis',
                    axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                        type: 'line'        // 默认为直线，可选为：'line' | 'shadow'
                    },
                    
                },
                legend: {
                    data: ['爱生活', '趣美味', '爱旅行', '爱电影', '爱保健'],
                    top:30
                },
                grid: {
                    left: '3%',
                    right: '4%',
                    bottom: '3%',
                    containLabel: true
                },
                xAxis: [
                    {
                        type: 'category',
                        data: ['一月', '二月', '三月', '四月']
                    }
                ],
                yAxis: [
                    {
                        type: 'value'
                    }
                ],
                color: ['#5885e8', '#13cfd5', '#00ce68', '#ff9565', '#20ff19'],
                series: [
                    {
                        name: '爱生活',
                        type: 'bar',
                        data: [320, 332, 301, 334]
                    },
                    {
                        name: '趣美味',
                        type: 'bar',
                        data: [220, 132, 101, 134]
                    },
                    {
                        name: '爱旅行',
                        type: 'bar',
                        data: [220, 182, 191, 234]
                    },
                    {
                        name: '爱电影',
                        type: 'bar',
                        data: [150, 232, 201, 154]
                    },
                    {
                        name: '爱保健',
                        type: 'bar',
                        data: [262, 118, 364, 426],
                    },
    
                ]
            };
    
    
            // 使用刚指定的配置项和数据显示图表。
            myChart2.setOption(option2);
        </script>
    </body>
    
    </html>



07-文章评论模块

- 该模块注意点
  - 1.分页插件使用流程与文章列表页面基本一致
  - 2.模板渲染需要使用分支语法，根据服务器返回的数据选择渲染按钮批准或者拒绝
  - 3.由于table中的br是动态添加，所以需要注册委托事件
  - 4.由于批准 和拒绝 和 删除接口都需要传递评论id，所以可以给三个按钮添加自定义属性data-id来存储对应评论的id



1.1-查询评论列表

- 1.添加分页插件HTML结构

    <div class="row text-center">
    <!-- <ul class="pagination pagination-sm">
      <li class="page-item first disabled"><a href="#" class="page-link">首页</a></li>
      <li class="page-item prev disabled"><a href="#" class="page-link">上一页</a></li>
      <li class="page-item active"><a href="#" class="page-link">1</a></li>
      <li class="page-item"><a href="#" class="page-link">2</a></li>
      <li class="page-item"><a href="#" class="page-link">3</a></li>
      <li class="page-item"><a href="#" class="page-link">4</a></li>
      <li class="page-item"><a href="#" class="page-link">5</a></li>
      <li class="page-item"><a href="#" class="page-link">6</a></li>
      <li class="page-item"><a href="#" class="page-link">7</a></li>
      <li class="page-item next"><a href="#" class="page-link">下一页</a></li>
      <li class="page-item last"><a href="#" class="page-link">尾页</a></li>
    </ul> -->
    <ul id="pagination" class="pagination-sm"></ul>

    $(function () {
          /* 1.页面一加载就搜索评论 */
          getCommentList(1);
    
          /**
          * @description:加载分页组件 
          * @param {type} totalPages ：总页数
          * @param {type} startPage ：当前页数
          * @return: 
          */
          function loadPagination(totalPages, startPage) {
            //(1)先销毁上一次的分页数据
            $('#pagination').twbsPagination('destroy');
            //(2)加载分页插件
            $('#pagination').twbsPagination({
              totalPages: totalPages,
              startPage: startPage,
              visiblePages: 6,
              first: '首页',
              prev: '上一页',
              next: '下一页',
              last: '尾页',
              onPageClick: function (event, page) {
                //如果点击的页数与当前页数不一致，则发送ajax请求
                if (page != startPage) {
                  getCommentList(page);
                };
              }
            });
          };
    
          /**
          * @description:文章评论搜索
          * @param {type} currentPage:当前页
          * @return: 
          */
          function getCommentList(currentPage) {
            $.ajax({
              url: BigNew.comment_list,
              type: 'get',
              dataType: 'json',
              data: {
                page: currentPage,
                perpage: 10,//每页返回10条数据
              },
              success: function (backData) {
                console.log(backData);
                //2.3 模板引擎渲染页面
                $('.table>tbody').html(template('comment_list', backData));
                //加载分页组件
                loadPagination(backData.data.totalPage, currentPage);
              }
            });
          };
    });

- 评论列表模板

    <!-- 评论列表模板 -->
      <script id="comment_list" type="text/html">
        {{ each data.data v}}
        <tr>
          <td>{{ v.author }}</td>
          <td>{{ v.content }}</td>
          <td>{{ v.title }}</td>
          <td>{{ v.date }}</td>
          <td>{{ v.state }}</td>
          <td class="text-center">
            {{ if v.state == '已通过' }}
            <a href="javascript:void(0);" data-id="{{ v.id }}" class="btn btn-warning btn-xs btn-reject">拒绝</a>
            {{ else if v.state == '待审核' }}
            <a href="javascript:void(0);" data-id="{{ v.id }}" class="btn btn-info btn-xs btn-pass">批准</a>
            {{ /if }}
            <a href="javascript:void(0);" data-id="{{ v.id }}" class="btn btn-danger btn-xs btn-delete">删除</a>
          </td>
        </tr>
        {{ /each }}
      </script>



1.2-评论审核通过

     /* 2.评论审核通过
      注意点：动态添加的元素需要注册委托事件
      */
      $('body').on('click', '.btn-pass', function () {
        //2.1 获取评论id
        var id = $(this).attr('data-id');
        //2.2 ajax请求
        $.ajax({
          url: BigNew.comment_pass,
          type: 'post',
          dataType: 'json',
          data: { id: id },
          success: function (backData) {
            console.log(backData);
            if(backData.code == 200){
              alert('审批通过');
              window.location.reload();
            }
          }
        });
      });



1.3-评论审核拒绝

    /* 3.评论审核拒绝
      注意点：动态添加的元素需要注册委托事件
      */
      $('body').on('click', '.btn-reject', function () {
        //2.1 获取评论id
        var id = $(this).attr('data-id');
        //2.2 ajax请求
        $.ajax({
          url: BigNew.comment_reject,
          type: 'post',
          dataType: 'json',
          data: { id: id },
          success: function (backData) {
            console.log(backData);
            if(backData.code == 200){
              alert('拒绝成功');
              window.location.reload();
            }
          }
        });
      });



1.4-评论审核删除

    /* 4.评论审核删除
          注意点：动态添加的元素需要注册委托事件
      */
      $('body').on('click', '.btn-delete', function () {
        //2.1 获取评论id
        var id = $(this).attr('data-id');
        //2.2 ajax请求
        $.ajax({
          url: BigNew.comment_delete,
          type: 'post',
          dataType: 'json',
          data: { id: id },
          success: function (backData) {
            console.log(backData);
            if (backData.code == 200) {
              alert('删除通过');
              window.location.reload();
            }
          }
        });
      });



08-新闻展示模块

- 由于本模块页面内容很多，但是整体难度不大，所以接口有限
- 由于课程时间有限，所以该模块不在课堂讲解。感兴趣的同学可以课后写一下
- 这里主要列出稍微有一点点难度的接口，介绍一下实现思路

1.1-首页index.html

1.1.1-热门焦点图

- 本页面技术难点
  - 1.第一张焦点图的样式不一样，有一个额外的类名first
    - 模板语法需要使用分支语法来渲染
  - 2.每一个新闻都有自己的id，可以利用window.location.href来传参



- 焦点图模板
  - $index == 0,如果是第一张图则添加类名first

    <!-- 导入模板引擎js文件 -->
    <script src="./js/template-web.js"></script>
    <!-- 引入jq文件 -->
    <script src="./js/jquery-1.12.4.min.js"></script>
    
    <!-- 1.热门焦点 -->
    <script id="focus_news" type="text/html">
        {{each data}}
            {{if $index == 0}}
                <li class="first">
            {{else}}
                <li>
            {{/if}}
                    <a href="./article.html?id={{$value.id}}"><img src="{{$value.cover}}" alt=""></a>
                    <p>{{$value.title}}</p>
                </li>
        {{/each}}
    </script>

- ajax请求

    <!-- 引入jq文件 -->
    <script src="./js/jquery-1.12.4.min.js"></script>
    <script>
        // 入口函数
        $(function () {
            //需求: 显示焦点新闻
            //1.一进到前台首页页面,就发送ajax请求,获取焦点新闻数据.
            $.ajax({
                type: 'get',
                url: 'http://localhost:8080/api/v1/index/hotpic',
                success: function (backData) {
                    console.log(backData);
                    //2.通过模板引擎渲染到前台首页上
                    //  第一个li标签添加first类.
                    var resHtml = template('focus_news', backData);
                    $('.focus_list').html(resHtml);
                }
            });
        });
    </script>



1.1.2-分类列表

    <!-- 2.分类列表模板 -->
    <script id="cat_list" type="text/html">
        <li class="up"></li>
        {{ each data v }}
        <li><a href="./list.html?id={{ v.id }}&name={{ v.name }}">{{ v.name }}</a></li>
        {{ /each }}
    </script>



    //2.加载分类列表
    $.ajax({
        type: 'get',
        url: 'http://localhost:8080/api/v1/admin/category/list',
        success: function (backData) {
            console.log(backData);
            $('.level_two').html(template('cat_list', backData));
        }
    });



1.1.3-最新资讯

    <!-- 3.最新资讯模板 -->
    <!-- 新闻列表模板 -->
    <script id="news" type="text/html">
        {{ each data v }}
        <div class="common_news_list">
            <a href="#" class="list_pic"><img src="{{ v.cover }}" alt=""></a>
            <h4><a href="./article.html?id={{ v.id }}">{{ v.title }}</a></h4>
            <p>{{@ v.intro }}</p>
            <div class="new_info">
                <span>标签：{{ v.category }}&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( {{ v.read }}
                    )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( {{ v.comments }} )</span>
                <b>{{ v.date }}</b>
            </div>
        </div>
        {{ /each }}
    </script>



    //3.最新咨询
    //ajax请求
    $.ajax({
        url: 'http://localhost:8080/api/v1/index/latest',
        type: 'get',
        dataType: 'json',
        success: function (backData) {
            console.log(backData);
            $('.common_news').html(template('news', backData));
        }
    });



1.1.4-热门排行

    <!-- 4.热门排行模板 -->
    <script id="rank" type="text/html">
        {{ each data v}}
        <li><span class="first">1</span><a href="./article.html?id={{ v.id }}">{{ v.title }}</a></li>
        {{ /each }}
    </script>

    //4.热门排行
    //ajax请求
    $.ajax({
        url: 'http://localhost:8080/api/v1/index/rank',
        type: 'get',
        dataType: 'json',
        success: function (backData) {
            console.log(backData);
            $('.hotrank_list').html(template('rank', backData));
        }
    });



1.1.5-最新评论

    <!-- 5.最新评论模板 -->
    <script id="comment" type="text/html">
        {{ each data v}}
        <li>
            <span>{{ v.author.substr(0,1) }}</span>
            <b><em>{{ v.author }}</em> {{ v.date }}说:</b>
            <strong>{{ v.intro }}</strong>
        </li>
        {{ /each }}
    </script>

    //5.最新评论
    $.ajax({
        url: 'http://localhost:8080/api/v1/index/latest_comment',
        type: 'get',
        dataType: 'json',
        success: function (backData) {
            console.log(backData);
            $('.comment_list').html(template('comment', backData));
        }
    });



1.1.6-焦点关注

    <!-- 6.焦点关注模板 -->
    <script id="guanzhu" type="text/html">
        {{ each data v}}
        <li>
            <a href="javascript:void(0);">{{ v.intro }}</a>
        </li>
        {{ /each }}
    </script>

    //6.焦点关注
    $.ajax({
        url: 'http://localhost:8080/api/v1/index/attention',
        type: 'get',
        dataType: 'json',
        success: function (backData) {
            console.log(backData);
            $('.guanzhu_list').html(template('guanzhu', backData));
        }
    });



完整代码

    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>大事件-首页</title>
        <link rel="stylesheet" href="css/reset.css">
        <link rel="stylesheet" href="css/font-awesome.min.css">
        <link rel="stylesheet" href="css/main.css">
    </head>
    
    <body>
        <div class="header">
            <a href="#" class="logo fl"><img src="images/logo.png" alt="logo"></a>
            <div class="search_form fl">
                <input type="text" class="search_txt" placeholder="黑洞照片">
                <button class="search_btn"><i class="fa fa-search"></i></button>
            </div>
            <div class="link_info fr">
                <a href="#"><i class="fa fa-angle-right"></i>&nbsp;&nbsp;网站地图</a><br>
                <a href="#"><i class="fa fa-angle-right"></i>&nbsp;&nbsp;招聘信息</a>
            </div>
        </div>
    
        <div class="menu_con">
            <div class="menu">
                <div class="menu_collapse fl">
                    <a href="javascript:;" class="level_one"><i class="fa fa-list-ul"></i>全部分类</a>
                    <ul class="level_two">
                        <li class="up"></li>
                        <li><a href="./list.html">最 新</a></li>
                        <li><a href="./list.html">科 技</a></li>
                        <li><a href="./list.html">股 市</a></li>
                        <li><a href="./list.html">商 品</a></li>
                        <li><a href="./list.html">外 汇</a></li>
                        <li><a href="./list.html">公 司</a></li>
                    </ul>
                </div>
                <ul class="left_menu fl">
                    <li><a href="#">最 新</a></li>
                    <li><a href="#">科 技</a></li>
                    <li><a href="#">股 市</a></li>
                    <li><a href="#">商 品</a></li>
                    <li><a href="#">外 汇</a></li>
                    <li><a href="#">公 司</a></li>
                </ul>
                <ul class="right_menu fr">
                    <li><a href="#">科技专题</a></li>
                    <li><a href="#">财经专题</a></li>
                </ul>
            </div>
        </div>
    
        <div class="main_con clearfix">
            <div class="left_con">
                <ul class="focus_list">
                    <li class="first">
                        <a href="#"><img src="images/pic01.jpg" alt=""></a>
                        <p>图片说明</p>
                    </li>
                    <li>
                        <a href="#"><img src="images/pic02.jpg" alt=""></a>
                        <p>图片说明图片说明图片说明图片说明图片说明图片说明图片说明图片说明图片说明</p>
                    </li>
                    <li>
                        <a href="#"><img src="images/pic03.jpg" alt=""></a>
                        <p>图片说明</p>
                    </li>
                    <li>
                        <a href="#"><img src="images/pic05.jpg" alt=""></a>
                        <p>图片说明</p>
                    </li>
                    <li>
                        <a href="#"><img src="images/pic06.jpg" alt=""></a>
                        <p>图片说明</p>
                    </li>
                </ul>
    
                <div class="common_title">
                    <h3><i class="fa fa-edit"></i> 最新资讯</h3>
                    <a href="#" class="more"><i class="fa fa-angle-double-right"></i></a>
                </div>
    
                <div class="common_news">
                    <div class="common_news_list">
                        <a href="#" class="list_pic"><img src="images/pic09.jpg" alt=""></a>
                        <h4><a href="#">亚马逊金融试水中国？背后却是和华尔街的“金融战争” </a></h4>
                        <p>亚马逊抛弃了中国的买家们，却难以割舍庞大的卖家市场，有消息指出亚马逊中国将针对中国庞大的卖家群体推出一项贷款推荐计划。在连续对印度、墨西哥等新兴市场大力推广线上支付服务后，亚马逊金融试水进军中国市场的信号终于被放出。
                        </p>
                        <div class="new_info">
                            <span>标签：财经&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( 5
                                )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( 10 )</span>
                            <b>发布于 2018-08-30 12:18:58</b>
                        </div>
                    </div>
    
                    <div class="common_news_list">
                        <a href="#" class="list_pic"><img src="images/pic03.jpg" alt=""></a>
                        <h4><a href="#">马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱,马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱</a></h4>
                        <p>亚马逊抛弃了中国的买家们，却难以割舍庞大的卖家市场，有消息指出亚马逊中国将针对中国庞大的卖家群体推出一项贷款推荐计划。在连续对印度、墨西哥等新兴市场大力推广线上支付服务后，亚马逊金融试水进军中国市场的信号终于被放出。
                        </p>
                        <div class="new_info">
                            <span>标签：财经&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( 5
                                )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( 10 )</span>
                            <b>发布于 2018-08-30 12:18:58</b>
                        </div>
                    </div>
    
                    <div class="common_news_list">
                        <a href="#" class="list_pic"><img src="images/pic05.jpg" alt=""></a>
                        <h4><a href="#">马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱</a></h4>
                        <p>亚马逊抛弃了中国的买家们，却难以割舍庞大的卖家市场，有消息指出亚马逊中国将针对中国庞大的卖家群体推出一项贷款推荐计划。在连续对印度、墨西哥等新兴市场大力推广线上支付服务后，亚马逊金融试水进军中国市场的信号终于被放出。
                        </p>
                        <div class="new_info">
                            <span>标签：财经&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( 5
                                )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( 10 )</span>
                            <b>发布于 2018-08-30 12:18:58</b>
                        </div>
                    </div>
    
                    <div class="common_news_list">
                        <a href="#" class="list_pic"><img src="images/pic07.jpg" alt=""></a>
                        <h4><a href="#">马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱</a></h4>
                        <p>亚马逊抛弃了中国的买家们，却难以割舍庞大的卖家市场，有消息指出亚马逊中国将针对中国庞大的卖家群体推出一项贷款推荐计划。在连续对印度、墨西哥等新兴市场大力推广线上支付服务后，亚马逊金融试水进军中国市场的信号终于被放出。
                        </p>
                        <div class="new_info">
                            <span>标签：财经&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( 5
                                )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( 10 )</span>
                            <b>发布于 2018-08-30 12:18:58</b>
                        </div>
                    </div>
    
                    <div class="common_news_list">
                        <a href="#" class="list_pic"><img src="images/pic10.jpg" alt=""></a>
                        <h4><a href="#">马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱</a></h4>
                        <p>亚马逊抛弃了中国的买家们，却难以割舍庞大的卖家市场，有消息指出亚马逊中国将针对中国庞大的卖家群体推出一项贷款推荐计划。在连续对印度、墨西哥等新兴市场大力推广线上支付服务后，亚马逊金融试水进军中国市场的信号终于被放出。
                        </p>
                        <div class="new_info">
                            <span>标签：财经&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( 5
                                )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( 10 )</span>
                            <b>发布于 2018-08-30 12:18:58</b>
                        </div>
                    </div>
                </div>
    
            </div>
            <div class="right_con">
                <div class="common_wrap">
                    <h3><i class="fa fa-bar-chart"></i> 一周热门排行</h3>
                    <ul class="content_list hotrank_list">
                        <li><span class="first">1</span><a href="#">神奇的水滴摄影，泡泡中的世界泡泡中的世界</a></li>
                        <li><span class="second">2</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span class="third">3</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>4</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>5</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>6</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>7</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                    </ul>
                </div>
                <div class="common_wrap">
                    <h3><i class="fa fa-commenting-o"></i> 最新评论</h3>
                    <ul class="content_list comment_list">
                        <li>
                            <span>山</span>
                            <b><em>山治</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>丽</span>
                            <b><em>丽丽</em> 9个月前(08-14)说:9个月前(08-14)说:9个月前(08-14)说:</b>
                            <strong>挺会玩的挺会玩的挺会玩的挺会玩的</strong>
                        </li>
                        <li>
                            <span>R</span>
                            <b><em>Rechael</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>J</span>
                            <b><em>John</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>段</span>
                            <b><em>段正淳</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>乔</span>
                            <b><em>乔峰</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                    </ul>
                </div>
                <div class="common_wrap">
                    <h3><i class="fa fa-bullseye"></i> 焦点关注</h3>
                    <ul class="content_list guanzhu_list">
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                    </ul>
                </div>
            </div>
        </div>
    
        <div class="footer clearfix">
            <div class="foot_link">
                <a href="#">关于我们</a>
                <span>|</span>
                <a href="#">联系我们</a>
                <span>|</span>
                <a href="#">招聘人才</a>
                <span>|</span>
                <a href="#">友情链接</a>
            </div>
            <p>CopyRight © 2019 深圳大事件信息技术股份有限公司 All Rights Reserved</p>
            <p>电话：0755-****888 粤ICP备*******8号</p>
        </div>
    
        <!-- 导入模板引擎js文件 -->
        <script src="./js/template-web.js"></script>
        <!-- 引入jq文件 -->
        <script src="./js/jquery-1.12.4.min.js"></script>
    
        <!-- 1.热门焦点 -->
        <script id="focus_news" type="text/html">
            {{each data}}
                {{if $index == 0}}
                    <li class="first">
                {{else}}
                    <li>
                {{/if}}
                        <a href="./article.html?id={{$value.id}}"><img src="{{$value.cover}}" alt=""></a>
                        <p>{{$value.title}}</p>
                    </li>
            {{/each}}
        </script>
    
        <!-- 2.分类列表 -->
        <script id="cat_list" type="text/html">
            <li class="up"></li>
            {{ each data v }}
            <li><a href="./list.html?id={{ v.id }}&name={{ v.name }}">{{ v.name }}</a></li>
            {{ /each }}
        </script>
    
        <!-- 3.最新资讯 -->
        <!-- 新闻列表模板 -->
        <script id="news" type="text/html">
            {{ each data v }}
            <div class="common_news_list">
                <a href="#" class="list_pic"><img src="{{ v.cover }}" alt=""></a>
                <h4><a href="./article.html?id={{ v.id }}">{{ v.title }}</a></h4>
                <p>{{@ v.intro }}</p>
                <div class="new_info">
                    <span>标签：{{ v.category }}&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( {{ v.read }}
                        )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( {{ v.comments }} )</span>
                    <b>{{ v.date }}</b>
                </div>
            </div>
            {{ /each }}
        </script>
    
        <!-- 4.热门排行 -->
        <script id="rank" type="text/html">
            {{ each data v}}
            <li><span class="first">1</span><a href="./article.html?id={{ v.id }}">{{ v.title }}</a></li>
            {{ /each }}
        </script>
    
        <!-- 5.最新评论 -->
        <script id="comment" type="text/html">
            {{ each data v}}
            <li>
                <span>{{ v.author.substr(0,1) }}</span>
                <b><em>{{ v.author }}</em> {{ v.date }}说:</b>
                <strong>{{ v.intro }}</strong>
            </li>
            {{ /each }}
        </script>
    
        <!-- 6.焦点关注 -->
        <script id="guanzhu" type="text/html">
            {{ each data v}}
            <li>
                <a href="javascript:void(0);">{{ v.intro }}</a>
            </li>
            {{ /each }}
        </script>
    
        <script>
            // 入口函数
            $(function () {
                //需求: 显示焦点新闻
                //1.一进到前台首页页面,就发送ajax请求,获取焦点新闻数据.
                $.ajax({
                    type: 'get',
                    url: 'http://localhost:8080/api/v1/index/hotpic',
                    success: function (backData) {
                        console.log(backData);
                        //2.通过模板引擎渲染到前台首页上
                        //  第一个li标签添加first类.
                        var resHtml = template('focus_news', backData);
                        $('.focus_list').html(resHtml);
                    }
                });
    
                //2.加载分类列表
                $.ajax({
                    type: 'get',
                    url: 'http://localhost:8080/api/v1/admin/category/list',
                    success: function (backData) {
                        console.log(backData);
                        $('.level_two').html(template('cat_list', backData));
                    }
                });
    
                //3.最新咨询
                //ajax请求
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/latest',
                    type: 'get',
                    dataType: 'json',
                    success: function (backData) {
                        console.log(backData);
                        $('.common_news').html(template('news', backData));
                    }
                });
    
                //4.热门排行
                //ajax请求
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/rank',
                    type: 'get',
                    dataType: 'json',
                    success: function (backData) {
                        console.log(backData);
                        $('.hotrank_list').html(template('rank', backData));
                    }
                });
    
                //5.最新评论
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/latest_comment',
                    type: 'get',
                    dataType: 'json',
                    success: function (backData) {
                        console.log(backData);
                        $('.comment_list').html(template('comment', backData));
                    }
                });
    
                //6.焦点关注
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/attention',
                    type: 'get',
                    dataType: 'json',
                    success: function (backData) {
                        console.log(backData);
                        $('.guanzhu_list').html(template('guanzhu', backData));
                    }
                });
    
    
            });
        </script>
    </body>
    
    </html>



1.2-新闻详情页面article.html



1.2.1-新闻详情展示



- 新闻详情模板

    <!-- 引入模板引擎js文件 -->
    <script src="./js/template-web.js"></script>
    <script src="./js/jquery-1.12.4.min.js"></script>
    
    <!-- 1.文章详情 -->
    <script id="author_list" type="text/html">
        {{data.author}} 发布于 {{data.date}}&nbsp;&nbsp;&nbsp;分类: {{data.category}}&nbsp;&nbsp;&nbsp;阅读:
                ({{data.read}})&nbsp;&nbsp;&nbsp;评论: ({{data.comments}})
    </script>

- 页面业务逻辑

    <script src="./js/jquery-1.12.4.min.js"></script>
    <script>
        // 入口函数
        $(function () {
            //需求: 显示点击过来的文章详情.
            //1.获取点击过来的文章id
            var id = window.location.search.split("=")[1];
            //2.根据id获取文章详情.
            $.ajax({
                type: 'get',
                url: 'http://localhost:8080/api/v1/index/article',
                data: {
                    id: id
                },
                success: function (backData) {
                    console.log(backData);
                    if (backData.code == 200) {
                        //3.把获取到的文章详情数据,填入到对应的标签中
                        $('.article_title').text(backData.data.title);
                        $('.article_con').html(backData.data.content);
                        var resH = template('author_list',backData);
                        $('.article_info').html(resH);
                    }
                }
            });
    
        });
    </script>



1.2.2-评论列表

    <!-- 2.评论列表模板 -->
    <script id="comment" type="text/html">
        {{ each data v }}
        <div class="comment_detail_list">
            <div class="person_pic fl">{{ v.author.substr(0,1) }}</div>
            <div class="name_time fl"><b>{{ v.author }}</b><span>{{ v.date }}</span></div>
            <div class="comment_text fl">
                {{ v.content }}
            </div>
        </div>
        {{ /each }}
    </script>

    //2.评论列表
    $.ajax({
        url: 'http://localhost:8080/api/v1/index/get_comment',
        type: 'get',
        dataType: 'json',
        data:{
            articleId:id
        },
        success: function (backData) {
            console.log(backData);
            $('.comment_list_con').html(template('comment', backData));
            //根据数组长度显示评论条数
            $('.comment_count').text(backData.data.length + '条评论');
        }
    });



1.2.3-发表评论

    //3.发表评论按钮点击事件
    //表单按钮：禁用默认事件
    $('.comment_sub').click(function (e) {
        //1.禁用默认行为
        e.preventDefault();
        //2.非空判断
        if ($('.comment_name').val().trim().length == 0 || $('.comment_input').val().trim().length == 0) {
            alert('请输入用户名和评论内容');
            return;
        };
        //3.ajax请求
        $.ajax({
            url: 'http://localhost:8080/api/v1/index/post_comment',
            type: 'post',
            dataType: 'json',
            data: {
                author: $('.comment_name').val(),
                content: $('.comment_input').val(),
                articleId: id
            },
            success: function (backData) {
                console.log(backData);
                if (backData.code == 201) {
                    alert('发表成功');
                    window.location.reload();
                }
            }
        });
    });



完整代码



    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>大事件-文章</title>
        <link rel="stylesheet" type="text/css" href="css/reset.css">
        <link rel="stylesheet" href="css/font-awesome.min.css">
        <link rel="stylesheet" type="text/css" href="css/main.css">
    </head>
    
    <body>
        <div class="header">
            <a href="#" class="logo fl"><img src="images/logo.png" alt="logo"></a>
            <div class="search_form fl">
                <input type="text" class="search_txt" placeholder="黑洞照片">
                <button class="search_btn"><i class="fa fa-search"></i></button>
            </div>
            <div class="link_info fr">
                <a href="#"><i class="fa fa-angle-right"></i>&nbsp;&nbsp;网站地图</a><br>
                <a href="#"><i class="fa fa-angle-right"></i>&nbsp;&nbsp;招聘信息</a>
            </div>
        </div>
    
        <div class="menu_con">
            <div class="menu">
                <div class="menu_collapse fl">
                    <a href="javascript:;" class="level_one"><i class="fa fa-list-ul"></i>全部分类</a>
                    <ul class="level_two">
                        <li class="up"></li>
                        <li><a href="#">最 新</a></li>
                        <li><a href="#">科 技</a></li>
                        <li><a href="#">股 市</a></li>
                        <li><a href="#">商 品</a></li>
                        <li><a href="#">外 汇</a></li>
                        <li><a href="#">公 司</a></li>
                    </ul>
                </div>
                <ul class="left_menu fl">
                    <li><a href="#">最 新</a></li>
                    <li><a href="#">科 技</a></li>
                    <li><a href="#">股 市</a></li>
                    <li><a href="#">商 品</a></li>
                    <li><a href="#">外 汇</a></li>
                    <li><a href="#">公 司</a></li>
                </ul>
                <ul class="right_menu fr">
                    <li><a href="#">科技专题</a></li>
                    <li><a href="#">财经专题</a></li>
                </ul>
            </div>
        </div>
    
        <div class="main_con clearfix">
            <div class="left_con setfr">
                <div class="breadcrumb">
                    当前位置： <a href="#">首页</a> &gt; <a href="#">科技</a> &gt; 文章详情
                </div>
                <h3 class="article_title">马斯克的新花样？连接人脑和电脑的初创公司又筹了一笔钱</h3>
                <div class="article_info">杰克 发布于 2019-05-08 07:08:46&nbsp;&nbsp;&nbsp;分类: 奇趣事&nbsp;&nbsp;&nbsp;阅读:
                    (98)&nbsp;&nbsp;&nbsp;评论: (0)</div>
                <div class="article_con">
    
                    <p>“上天入地”还不够？硅谷“钢铁侠”马斯克名下一家低调的公司最近又融了一大笔钱。</p>
    
                    <p>马斯克投资的一家小型且不为人熟知的脑机接口（brain-computer
                        interface）公司Neuralink披露，该公司融到了3900万美元，虽然不及公司计划的融资额5100万美元，但也比上次的融资多了不少。</p>
    
                    <p>两年前该公司曾计划融资1亿美元，但只拿到了2700万美元的融资。</p>
    
                    <p>在Neuralink递交给美国证券交易委员会（SEC）的材料中，并没有披露融资是否来自传统的风险投资者或者来自该公司的员工。</p>
    
                    <p>2016年，Neuralink在美国加利福尼亚州以医学研究公司的名义成立，之后一直低调运行。</p>
    
                </div>
                <div class="article_links">
                    上一篇： <a href="#">世界第一台可以玩游戏的冰箱</a><br>
                    下一篇： <a href="#">世界第一个可以玩游戏的马桶、世界上第一个可以玩游戏的茶杯</a>
                </div>
    
                <form action="" class="comment_form">
                    <div class="form_group">
                        <label>用户名：</label>
                        <input type="text" placeholder="请输入用户名" class="comment_name">
                    </div>
                    <div class="form_group">
                        <label>评论内容：</label>
                        <textarea placeholder="请发表您的评论" class="comment_input"></textarea>
                    </div>
                    <div class="form_group">
                        <input type="submit" name="" value="评 论" class="comment_sub"></div>
                </form>
    
                <div class="comment_count">
                    4条评论
                </div>
    
                <div class="comment_list_con">
                    <div class="comment_detail_list">
                        <div class="person_pic fl">乔</div>
                        <div class="name_time fl"><b>虚竹</b><span>4小时前</span></div>
                        <div class="comment_text fl">
                            遏制茅台酒价格过快上涨，多渠道供给，就不一定要买，租茅台酒也可以的，租售同权。开发共有产权茅台酒，让老百姓喝得起茅台酒，饮者有其酒。
                        </div>
                    </div>
                    <div class="comment_detail_list">
                        <div class="person_pic fl">段</div>
                        <div class="name_time fl"><b>虚竹</b><span>4小时前</span></div>
                        <div class="comment_text fl">
                            遏制茅台酒价格过快上涨，多渠道供给，就不一定要买，租茅台酒也可以的，租售同权。开发共有产权茅台酒，让老百姓喝得起茅台酒，饮者有其酒。
                        </div>
                    </div>
                    <div class="comment_detail_list">
                        <div class="person_pic fl">虚</div>
                        <div class="name_time fl"><b>虚竹</b><span>4小时前</span></div>
                        <div class="comment_text fl">
                            遏制茅台酒价格过快上涨，多渠道供给，就不一定要买，租茅台酒也可以的，租售同权。开发共有产权茅台酒，让老百姓喝得起茅台酒，饮者有其酒。
                        </div>
                    </div>
                    <div class="comment_detail_list">
                        <div class="person_pic fl">王</div>
                        <div class="name_time fl"><b>虚竹</b><span>4小时前</span></div>
                        <div class="comment_text fl">
                            遏制茅台酒价格过快上涨，多渠道供给，就不一定要买，租茅台酒也可以的，租售同权。开发共有产权茅台酒，让老百姓喝得起茅台酒，饮者有其酒。
                        </div>
                    </div>
                </div>
            </div>
            <div class="right_con setfl">
                <div class="common_wrap">
                    <h3><i class="fa fa-bar-chart"></i> 一周热门排行</h3>
                    <ul class="content_list">
                        <li><span class="first">1</span><a href="#">神奇的水滴摄影，泡泡中的世界泡泡中的世界</a></li>
                        <li><span class="second">2</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span class="third">3</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>4</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>5</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>6</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><span>7</span><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                    </ul>
                </div>
                <div class="common_wrap">
                    <h3><i class="fa fa-commenting-o"></i> 最新评论</h3>
                    <ul class="content_list comment_list">
                        <li>
                            <span>山</span>
                            <b><em>山治</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>丽</span>
                            <b><em>丽丽</em> 9个月前(08-14)说:9个月前(08-14)说:9个月前(08-14)说:</b>
                            <strong>挺会玩的挺会玩的挺会玩的挺会玩的</strong>
                        </li>
                        <li>
                            <span>R</span>
                            <b><em>Rechael</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>J</span>
                            <b><em>John</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>段</span>
                            <b><em>段正淳</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                        <li>
                            <span>乔</span>
                            <b><em>乔峰</em> 9个月前(08-14)说:</b>
                            <strong>挺会玩的</strong>
                        </li>
                    </ul>
                </div>
                <div class="common_wrap">
                    <h3><i class="fa fa-bullseye"></i> 焦点关注</h3>
                    <ul class="content_list guanzhu_list">
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                        <li><a href="#">神奇的水滴摄影，泡泡中的世界</a></li>
                    </ul>
                </div>
            </div>
        </div>
    
        <div class="footer clearfix">
            <div class="foot_link">
                <a href="#">关于我们</a>
                <span>|</span>
                <a href="#">联系我们</a>
                <span>|</span>
                <a href="#">招聘人才</a>
                <span>|</span>
                <a href="#">友情链接</a>
            </div>
            <p>CopyRight © 2019 深圳大事件信息技术股份有限公司 All Rights Reserved</p>
            <p>电话：0755-****888 粤ICP备*******8号</p>
        </div>
    
        <!-- 引入模板引擎js文件 -->
        <script src="./js/template-web.js"></script>
        <script src="./js/jquery-1.12.4.min.js"></script>
    
        <!-- 1.文章详情 -->
        <script id="author_list" type="text/html">
            {{data.author}} 发布于 {{data.date}}&nbsp;&nbsp;&nbsp;分类: {{data.category}}&nbsp;&nbsp;&nbsp;阅读:
                    ({{data.read}})&nbsp;&nbsp;&nbsp;评论: ({{data.comments}})
        </script>
    
        <!-- 2.评论列表 -->
        <script id="comment" type="text/html">
            {{ each data v }}
            <div class="comment_detail_list">
                <div class="person_pic fl">{{ v.author.substr(0,1) }}</div>
                <div class="name_time fl"><b>{{ v.author }}</b><span>{{ v.date }}</span></div>
                <div class="comment_text fl">
                    {{ v.content }}
                </div>
            </div>
            {{ /each }}
        </script>
    
        <script>
            // 入口函数
            $(function () {
                //需求: 显示点击过来的文章详情.
                //1.获取点击过来的文章id
                var id = window.location.search.split("=")[1];
                //2.根据id获取文章详情.
                $.ajax({
                    type: 'get',
                    url: 'http://localhost:8080/api/v1/index/article',
                    data: {
                        id: id
                    },
                    success: function (backData) {
                        console.log(backData);
                        if (backData.code == 200) {
                            //3.把获取到的文章详情数据,填入到对应的标签中
                            $('.article_title').text(backData.data.title);
                            $('.article_con').html(backData.data.content);
                            var resH = template('author_list', backData);
                            $('.article_info').html(resH);
                        }
                    }
                });
    
                //2.评论列表
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/get_comment',
                    type: 'get',
                    dataType: 'json',
                    data: {
                        articleId: id
                    },
                    success: function (backData) {
                        console.log(backData);
                        $('.comment_list_con').html(template('comment', backData));
                        //根据数组长度显示评论条数
                        $('.comment_count').text(backData.data.length + '条评论');
                    }
                });
    
                //3.发表评论按钮点击事件
                //表单按钮：禁用默认事件
                $('.comment_sub').click(function (e) {
                    //1.禁用默认行为
                    e.preventDefault();
                    //2.非空判断
                    if ($('.comment_name').val().trim().length == 0 || $('.comment_input').val().trim().length == 0) {
                        alert('请输入用户名和评论内容');
                        return;
                    };
                    //3.ajax请求
                    $.ajax({
                        url: 'http://localhost:8080/api/v1/index/post_comment',
                        type: 'post',
                        dataType: 'json',
                        data: {
                            author: $('.comment_name').val(),
                            content: $('.comment_input').val(),
                            articleId: id
                        },
                        success: function (backData) {
                            console.log(backData);
                            if (backData.code == 201) {
                                alert('发表成功');
                                window.location.reload();
                            }
                        }
                    });
                });
    
            });
        </script>
    </body>
    
    </html>



1.3-新闻列表页面list.html





    <!-- 导包template -->
    <script src="./js/template-web.js"></script>
    <!-- 新闻列表模板 -->
    <script id="news" type="text/html">
        {{ each data.data v }}
        <div class="common_news_list">
            <a href="#" class="list_pic"><img src="{{ v.cover }}" alt=""></a>
            <h4><a href="./article.html?id={{ v.id }}">{{ v.title }}</a></h4>
            <p>{{@ v.content }}</p>
            <div class="new_info">
                <span>标签：{{ v.category }}&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;<i class="fa fa-eye"></i>&nbsp;阅读( {{ v.read }}
                    )&nbsp;&nbsp;&nbsp;<i class="fa fa-commenting"></i>&nbsp;评论( {{ v.comments }} )</span>
                <b>{{ v.date }}</b>
            </div>
        </div>
        {{ /each }}
    </script>
    
    <script>
        //入口函数
        $(function () {
            //搜索按钮点击事件
            $('.search_btn').click(function () {
                //获取搜索内容
                var text = $('.search_txt').val();
                //ajax请求
                $.ajax({
                    url: 'http://localhost:8080/api/v1/index/search',
                    type: 'get',
                    dataType: 'json',
                    data: {
                        page: 1,
                        perpage: 10,
                        key: text
                    },
                    success: function (backData) {
                        console.log(backData);
                        $('.setfr').html(template('news', backData));
                    }
                });
            });
    
            //页面一加载：默认搜索全部
            $('.search_btn').trigger('click');
        });
    </script>







09-补充

1.1 jQ的serialize方法

作用: 也是可以把form表单里面带有name属性的标签的值取出来,组成key=value&key2=value2这种格式的字符串.

和FormData的区别:

	1.FormData是原生js就有的,jQuery他也封装了,可以用 ;   serialize()是jQuery提供的方法.

	2.FormData对象需要创建,传入是form表单dom对象,      serialize()直接是form表单jQuery对象调用.

	3.FormData不需要设置请求头.

	4.FormData传递过去的是一个formData对象,  而serialize()传过去的是一个字符串.

	5.FormData可以传递文件, 但是serialize()不行.

1.2 .ajax()/.get()/$.post()补充

传递参数有几种方式:

	 1.key1=value1&key2=value2&key3=value3;

	 2.FormData对象

 	3.serialize()方法序列化后的值.

     4.我们自己写的对象

            data: {

                   id:9,

                   name:'鹏鹏',

                   slug:'pengpengsba'

            }



1.3  url编码的方法

  decodeURI('urlencode格式字符串');  //解码

  encodeURI('字符串');  //编码

			


