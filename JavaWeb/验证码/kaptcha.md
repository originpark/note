# 验证码作用

使用验证码可以解决表单重复提交的问题。

当客户端在表单中点击获取验证码时，服务器生成一个验证码字符串，并保存在session中，并且在客户端上显示该验证码，此后用户输入验证码点击提交，服务器收到请求后，先获取session中的验证码，然后将验证码删除，接着比较session中的验证码和用户输入的是否相同，如果不相同就阻止后续行为。这样，在用户下一次点击获取验证码之前，用户只能提交一次，因为第二次提交时session中的验证码已经为null





# kaptcha

kaptcha是谷歌提供的设置验证码的工具类，将相应的jar包导入后即可使用。



该jar包中有一个kaptchaServlet，访问该servlet可以生成一个随机验证码图片，我们只需要为该servlet在xml中配置访问地址即可。

```xml
<servlet>
    <servlet-name>kaptchaTestServlet</servlet-name>
    <servlet-class>com.origin.web.kaptchaTestServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>kaptchaTestServlet</servlet-name>
    <url-pattern>/kaptcha.jpg</url-pattern>
</servlet-mapping>
```



在需要验证码的地方加入一个img标签，src为kaptchaServlet的路径

```html
<img src="kaptcha.jpg" alt="" class="kaptcha">
```



在js中设置点击验证码图片更新验证码

```javascript
$(function () {
    $(".kaptcha").on({
        click: function () {
            //this.src表示重新设置src值，相当于重新访问kaptcha，即会重新生成验证码
            //由于浏览器存在缓存，因此给src设置一个时间戳参数，以保证每次的src都不相同
            this.src = "${sessionScope.basePath}kaptcha.jpg?d=" + new Date();
        }
    })
})
```







