# GithubOauth2Example

Create github auth application : Visit https://github.com/settings/applications/new and follow screenshot.

![image](https://user-images.githubusercontent.com/25124224/114698548-da3d9680-9d3c-11eb-95c8-638f55e6f521.png)


To enable Spring Security OAuth 2.0, we need to add the following starter:
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-client</artifactId>
        </dependency>
        


Now, we’ll need to modify our application.yml:
spring:
  security:
   oauth2:
     client:
       registration:
         github:
           clientId: ${GITHUB_CLIENT_ID}
           clientSecret: ${GITHUB_CLIENT_SECRET}
           

The GITHUB_CLIENT_ID and the GITHUB_CLIENT_SECRET are environment variables that hold the values that you get back once you register your application on GitHub (same for Google, Facebook, or any other provider).


Now let’s configure our security:

[@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .anyRequest().authenticated()
            .and()
            .oauth2Login();
    }
}
](url)

In the above code, we want every request to be authenticated. We add oauth2Login in order to configure authentication support using OAuth 2.0.

Now if we try to access localhost:8080 in our browser, we’ll be forwarded to the GitHub sign-in page:
![image](https://user-images.githubusercontent.com/25124224/114699647-33f29080-9d3e-11eb-8681-c84fbdac33f0.png)

So what happened here?
When a request is made to localhost:8080, Spring security will try to find an authenticated object, but eventually, it fails to. So it redirects to:
http://localhost:8080/oauth2/authorization/github

Internally, this request is getting handled by OAuth2AuthorizationRequestRedirectFilter, which uses implements doFilterInternal that matches against the /oauth2/authorization/github URI and redirect the request to
https://github.com/login/oauth/authorize?response_type=code&client_id=<clientId>&scope=read:user&state=<state>&redirect_uri=http://localhost:8080/login/oauth2/code/github
the above redirect_uri contains the same value we put when we registered our application.
  
  
Conclusion
It’s very simple to build an application with “social login” using OAuth 2.0 and Spring Boot.
