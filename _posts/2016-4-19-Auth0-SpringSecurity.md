---
layout: post
title: Auth0 and Spring Security 4
---

I recently decided to include Auth0 (https://auth0.com/) in one of my Spring Boot MVC projects. Auth0 is quick to setup and us, has a range of sample projects for from end development (i.e. within Angular / Ionic etc) and for backed services.

What I did have trouble with was the Spring Security configuration. This was probably a result of Spring Security 4 no (by default) imposing CSRF protection. In my application I didnt need that kind of overhead, given I wasnt using cookies at all, and actually complicated things if anything.

The key was ensuring I DIDNT include the auth0-security.context.xml file in my Spring configuration (which I was doing by mistake based on earlier tutorials). If you implement the configuration in a class instead, you can override and disable the csrf checking with ease. Namely,

public class SecurityConfig extends WebSecurityConfigurerAdapter {
  //....
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .csrf().disable()
                    .addFilterAfter(auth0AuthenticationFilter(auth0AuthenticationEntryPoint()), SecurityContextPersistenceFilter.class)
                    .addFilterBefore(simpleCORSFilter(), Auth0AuthenticationFilter.class)
                    .antMatcher("/**")
                    .authorizeRequests()
                    .antMatchers(securedRoute).authenticated();
        }
}

Project sample avaliable here: https://github.com/mbritten/sample-auth0-springboot
