问题

vue登陆请求http://127.0.0.1:8081/yangllong_ssm/login.do

```java
@RestController
public class VueController {
    @RequestMapping("/login")
    public String login(
            @RequestParam(value="username",required = false) String username,
            @RequestParam(value="password",required = false) String password, Model model
            ){
        System.out.println("username:"+username+"password:"+password);

      
        if (username.equals("admin") && password.equals("123")){
            System.out.println("登陆成功");
            return "index";}else{
            model.addAttribute("error","用户登陆失败，错误信息：");
            return "login";
        }
    }

}

```

```js
methods: {
    submitForm(login) {
      this.$refs[login].validate(valid => {
        if (valid) {
          //提交表单
          this.$http
            .post("http://127.0.0.1:8081/yangllong_ssm/login.do", {
              username: this.login.username,
              password: this.login.password
            })
            .then(result => {
              console.log(result);
              if (result.bodyText === "index") {
                this.$router.push({ path: "home" }); //跳转到home组件中
              } else {
                console.log("登录失败");
                return false;
              }
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    }
  }
```

我之前写的@Controller，返回的一直是jsp页面，报错404

要用@RestController注解！

解决跨域问题