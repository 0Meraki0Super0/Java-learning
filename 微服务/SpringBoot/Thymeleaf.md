
### 简介

1. 是一种**服务器渲染技术**，其页面数据在服务器渲染
2. 是一个Java类库，是xml/html的模板引擎，可用作mvc的view层
3. 无需服务器渲染也可以被浏览器运行
4. 不是高性能引擎，不适用高并发应用

### 使用实例

- 编写控制器，将请求转发到登录页面
```java
@Controller
public class IndexController {

    //编写方法，转发到登录页面
    @GetMapping(value = {"/", "/login"})
    public String login() {
        /**
        因为引入了starter-thymeleaf
        这里就会直接使用视图解析到thymeleaf下的模板文件adminLogin.html，而不是resources-templates下的静态资源
         */
        return "adminLogin";
    }
}
```
- 控制器，响应用户的登录请求
```java
@Controller
@Slf4j
public class AdminController {

    //响应用户的登录请求。注意，此处和上方的请求形式不同
    @PostMapping("/login")
    public String login(Admin admin, HttpSession session, Model model) {

        //验证用户是否合法
        if (StringUtils.hasText(admin.getName()) && "666".equals(admin.getPassword())) {

            //将登录用户保存到session。键值对k-v
            session.setAttribute("loginAdmin", admin);

            //合法, 重定向到manage.html
            //不使用请求转发是防止刷新页面会重复提交
            //manage.html：因为可以更加明确的表示到哪个页面
            //manage.html表示要去找控制器中映射路径为 manage.html的方法
            return "redirect:/manage.html";
        } else {
            //不合法，就重新登录, 请求转发
            model.addAttribute("msg", "账号/用户错误");
            return "adminLogin";
        }
    }


    //处理用户的请求到 manage.html
    @GetMapping("/manage.html")//
    public String mainPage(Model model, HttpSession session) {

        log.info("进入mainPage()");
        //可以这里集合-模拟用户数据, 放入到request域中，并显示
        ArrayList<User> users = new ArrayList<>();
        users.add(new User(1, "关羽~", "666666", 20, "gy@sohu.com"));

        //将数据放入到request域
        model.addAttribute("users", users);

        return "manage"; //这里才是我们的视图解析到 /templates/manage.html
		//使用方法验证，阻止非法登录
        //Object loginAdmin = session.getAttribute("loginAdmin");
        //if(null != loginAdmin) {//说明成功登录过
        //    //可以这里集合-模拟用户数据, 放入到request域中，并显示
        //    ArrayList<User> users = new ArrayList<>();
        //    users.add(new User(1, "关羽~", "666666", 20, "gy@sohu.com"));
        //    //将数据放入到request域
        //    model.addAttribute("users", users);
        //    return "manage"; //这里才是我们的视图解析到 /templates/manage.html
        //} else {
        //    //这里就返回登录页，并给出提示
        //    model.addAttribute("msg","你没有登录/请登录");
        //    return "adminLogin";//请求转发到adminLogin.html
        //}
    }
}
```
- 编写拦截器，拦截非法请求
  ==执行顺序：pre--目标方法--post--渲染视图--after==
```java
@Slf4j
public class LoginInterceptor implements HandlerInterceptor {
    @Override //目标方法前被调用
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //为了让小伙伴看到访问的URI
        String requestURI = request.getRequestURI();
        String requestURL = request.getRequestURL().toString();
        log.info("preHandle拦截到的请求的URI={}", requestURI);
        log.info("preHandle拦截到的请求的URL={}", requestURL);
        //进行登录的校验
        HttpSession session = request.getSession();
        Object loginAdmin = session.getAttribute("loginAdmin");
        if (null != loginAdmin) {//说明该用户已经成功登录
            //放行
            return true;
        }
        //拦截, 重新返回到登录页面
        request.setAttribute("msg", "你没有登录/请登录~~");
        request.getRequestDispatcher("/").forward(request, response);
        return false;
    }


    @Override //目标方法后调用
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("postHandle执行了...");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        log.info("afterCompletion执行了...");
    }
}
```
- 使拦截器生效：
	- 方一：编写webConfig配置类，注入bean WebMvcConfigurer
	- 方二：编写配置类实现WebMvcConfigurer接口，调用增加拦截器方法
		*注：`WebMvcConfigurer` 接口是 Spring MVC 提供的用于配置 MVC 的接口，其中包含了一系列配置方法，包括配置拦截器的方法*
	```java
	@Configuration
public class WebConfig /*implements WebMvcConfigurer*/ {

    //@Override
    //public void addInterceptors(InterceptorRegistry registry) {
    //
    //    //注册自定义拦截器LoginInterceptor
    //    registry.addInterceptor(new LoginInterceptor())
    //            .addPathPatterns("/**") //拦截所有的请求
    //    .excludePathPatterns("/","/login","/images/**");//指定要放行的，后面可以根据业务需求，来添加放行的请求路径
    //}

    @Bean
    public WebMvcConfigurer webMvcConfigurer() {

        return new WebMvcConfigurer() {
            @Override
            public void addInterceptors(InterceptorRegistry registry) {
                System.out.println("addInterceptors~~~");
                //注册拦截器
                registry.addInterceptor(new LoginInterceptor())
                        .addPathPatterns("/**") .excludePathPatterns ("/","/login","/images/**","/upload.html","/upload","/sql");
            }
        };
    }
}
```
