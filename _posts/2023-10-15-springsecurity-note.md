---
layout: post
title: springsecurity and jwt 项目使用
date: 2023-10-15 11:30:00 +0800
category: SpringSecurity
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/project.jpg
icon: note
---


* content
{:toc}
# springsecurity 项目使用

## 首先springsecurity 配置都在一个配置类中
1. 配置自定义的用户查找service userdataileservice 

```
@Service
public class UserDetailServiceImpl implements UserDetailsService {

    @Autowired
    private ISysUserService iSysUserService;

    @Autowired
    private ISysRoleService iSysRoleService;

    /**
     * 完成账号校验‘
     * @param username
     * @return
     * @throws UsernameNotFoundException
     */
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //1.根据username做查询
        List<SysUser> list = iSysUserService.queryByUserName(username);

        if(list!=null &&list.size()==1){
            SysUser sysUser = list.get(0);

            //根据当前登录的账号信息查询到角色信息

            List<SysRole> sysRoles = iSysRoleService.queryByUserId(sysUser.getUserId());
            List<GrantedAuthority> listRole = new ArrayList<>();

            if(sysRoles!=null && sysRoles.size()>0){
                for (SysRole sysRole : sysRoles) {
                    listRole.add(new SimpleGrantedAuthority(sysRole.getRoleName()));
                }
            }
            UserDetails userDetails = new User(sysUser.getUsername(),sysUser.getPassword(),listRole);
            return userDetails;
        }

        return null;
    }
}
```

2. 配置一个密码处理


3.写一个filter 继承UsernamePasswordAuthenticationFilter

```
package com.wanshu.sys.filter;

import com.alibaba.fastjson.JSON;
import com.wanshu.common.constant.SystemConstant;
import com.wanshu.common.result.JWTUtils;
import com.wanshu.sys.entity.SysUser;
import org.apache.catalina.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.HashMap;
import java.util.Map;

/**
 * @Description token过滤器
 * @Author zwf
 * @Date 2023/9/2 11:11
 */
public class TokenLoginFilter extends UsernamePasswordAuthenticationFilter {

    private AuthenticationManager authenticationManager;

    public  TokenLoginFilter(AuthenticationManager authenticationManager){
        this.authenticationManager = authenticationManager;
    }

    /**
     * 具体的认证方法
     * @param request
     * @param response
     * @return
     * @throws AuthenticationException
     */
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {

        SysUser sysUser = null;

        //获取表单数据
        //判断是否登录成功
        try {
            String loginInfo = getRequestJSON(request);

            sysUser = JSON.parseObject(loginInfo,SysUser.class);

            UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(sysUser.getUsername(),sysUser.getPassword());

            return authenticationManager.authenticate(authenticationToken);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    private String getRequestJSON(HttpServletRequest request) throws IOException {
        BufferedReader streamReader = new BufferedReader(new InputStreamReader(request.getInputStream()));

        StringBuilder sb = new StringBuilder();

        String inputStr = null;

        while((inputStr = streamReader.readLine())!=null){
            sb.append(inputStr);
        }
        return sb.toString();
    }

    /**
     * 登录成功的方法
     * @param request
     * @param response
     * @param chain
     * @param authResult
     * @throws IOException
     * @throws ServletException
     */
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
        //生成token信息
        Map<String,String> map= new HashMap<>();

        map.put("username",authResult.getName());

        //可以存储角色

        String token = JWTUtils.getToken(map);

        //需要将token响应给客户端
        response.addHeader("Authorization", SystemConstant.SYS_TOKEN_PREFIX +token);

        response.addHeader("Access-Control-Expose-Headers","Authorization");
        response.setContentType("application/json;charset=utf-8");
        response.setStatus(HttpServletResponse.SC_OK);
        PrintWriter writer = response.getWriter();
        Map<String,Object> resultMap = new HashMap<>();
        resultMap.put("code",HttpServletResponse.SC_OK);
        resultMap.put("msg","认证通过");

        writer.write(JSON.toJSONString(resultMap));

        writer.flush();
        writer.close();
    }

    /**
     * 登录失败
     * @param request
     * @param response
     * @param failed
     * @throws IOException
     * @throws ServletException
     */
    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) throws IOException, ServletException {
        response.setContentType("application/json;charset=utf-8");
        response.setStatus(HttpServletResponse.SC_OK);
        PrintWriter writer = response.getWriter();
        Map<String,Object> resultMap = new HashMap<>();
        resultMap.put("code",HttpServletResponse.SC_UNAUTHORIZED);
        resultMap.put("msg","用户名或密码错误！");

        writer.write(JSON.toJSONString(resultMap));

        writer.flush();
        writer.close();
    }
}

```

4.在attempAuthenication中写认证方法


5.从request中获取login的userinfo 

6.使用UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(sysUser.getUsername(),sysUser.getPassword());

             authenticationManager.authenticate(authenticationToken);
这里通过和userservice中查找到的user对比来实现查找

7.对比成功执行successfulAuthentication方法，反之执行unsuccessfulAuthentication

8.建立一个配置类，实现WebSecurityConfigurerAdapter，在这里启动userservice 密码处理器,过滤器filter添加，跨域配置

```
@Configuration
public class MySpringSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Autowired
    UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService) // 绑定自定义的认证Service
                .passwordEncoder(new BCryptPasswordEncoder()); // 绑定密码处理器
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests()
                .antMatchers("/doc.html","/doc.html/**","/webjars/**","/v2/**","/v3/**","/swagger-resources","/swagger-resources/**","/swagger-ui.html","/swagger-ui.html/**").permitAll()
                .antMatchers("/api/*/auth/**","/test/**").permitAll()
                .anyRequest().authenticated()
                .and()
                // 设置跨域的处理
                .cors().configurationSource(corsConfigurationSource())
                .and()
                .addFilter(new TokenLoginFilter(super.authenticationManager())) // 绑定认证的接口
                .addFilter(new TokenVerifyFilter(super.authenticationManager())) // 绑定校验的接口
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }


    /**
     * 设置跨域的信息
     * @return
     */
    CorsConfigurationSource corsConfigurationSource(){
        CorsConfiguration config = new CorsConfiguration();
        // 配置跨域拦截的相关信息
        config.setAllowedHeaders(Arrays.asList("*"));
        config.setAllowedMethods(Arrays.asList("*"));
        config.setAllowedOrigins(Arrays.asList("*"));
        config.setMaxAge(3600l);
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        //拦截所有请求，加入配置
        source.registerCorsConfiguration("/**",config);
        return source;
    }

}
```

## jwt 工具包

```
/**
 * JWT操作的工具类
 */
public class JWTUtils {

    private static final String SECERT = "xxx";

    /**
     * 生成Token信息
     * @return
     */
    public static String getToken(Map<String,String> map){
        JWTCreator.Builder builder = JWT.create();
        // 设置 payload
        map.forEach((k,v)->{
            builder.withClaim(k,v);
        });
        // 设置过期时间
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.DATE,7); // 默认的过期时间是7天
        Map<String,Object> header = new HashMap<>();
        header.put("alg","HS256");
        header.put("typ","JWT");
        return builder.withHeader(header)
                .withExpiresAt(calendar.getTime())
                .sign(Algorithm.HMAC256(SECERT));
    }

    /**
     * 验证Token
     * @return
     *     DecodedJWT  可以用来获取用户信息
     */
    public static DecodedJWT verify(String token){
        // 如果不抛出异常说明验证通过，否则验证失败
        DecodedJWT verify = null;
        try {
            verify = JWT.require(Algorithm.HMAC256(SECERT)).build().verify(token);
        }catch (SignatureVerificationException e){
            e.printStackTrace();
        }catch (AlgorithmMismatchException e){
            e.printStackTrace();
        }catch (Exception e){
            e.printStackTrace();
        }
        return verify;
    }
}
```
