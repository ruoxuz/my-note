# 一个项目同时配置多套spring security

配置类

    package jp.co.slab.ecsite.common.config;

    import jp.co.slab.ecsite.common.util.Sha256PasswordEncoder;
    import jp.co.slab.ecsite.service.impl.AdminUserDetailsServiceImpl;
    import jp.co.slab.ecsite.service.impl.UserDetailsServiceImpl;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.core.annotation.Order;
    import org.springframework.http.HttpMethod;
    import org.springframework.security.authentication.AuthenticationManager;
    import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.builders.WebSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.config.http.SessionCreationPolicy;
    import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

    @Configuration
    @EnableWebSecurity
    public class SecurityConfig {

        @Order(1)
        @Configuration
        public static class FrontConfiguration extends WebSecurityConfigurerAdapter {
            @Autowired
            private UserDetailsServiceImpl userDetailsService;

            @Autowired
            private Sha256PasswordEncoder passwordEncoder;

            @Override
            protected void configure(HttpSecurity http) throws Exception {
                http.antMatcher("/api/**")
                        .cors().and().csrf().disable()
                        .authorizeRequests()
                        .antMatchers("/api/auth/*").permitAll()
                        .anyRequest().authenticated()
                        .and()
                        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                        .and()
                        .addFilterBefore(jwtRequestFilter(), UsernamePasswordAuthenticationFilter.class);
            }

            @Override
            public void configure(AuthenticationManagerBuilder authenticationManagerBuilder) throws Exception {
                authenticationManagerBuilder
                        .userDetailsService(userDetailsService)
                        .passwordEncoder(passwordEncoder);
            }

            @Bean
            @Override
            public AuthenticationManager authenticationManagerBean() throws Exception {
                return super.authenticationManagerBean();
            }

            @Bean
            public JwtRequestFilter jwtRequestFilter() {
                return new JwtRequestFilter();
            }
        }

        @Order(2)
        @Configuration
        public static class AdminConfiguration extends WebSecurityConfigurerAdapter {

            @Autowired
            private AdminUserDetailsServiceImpl userDetailsService;

            @Autowired
            private Sha256PasswordEncoder passwordEncoder;

            @Override
            protected void configure(HttpSecurity http) throws Exception {
                http.antMatcher("/admin/**")
                        .csrf()
                        .and()
                        .authorizeRequests()
                        .antMatchers("/admin/**").hasRole("ADMIN")
                        .and()
                        .formLogin()
                        .usernameParameter("account").passwordParameter("password")
                        .loginPage("/admin/login")
                        .permitAll()
                        .defaultSuccessUrl("/admin/dashboard")
                        .and()
                        .logout()
                        .logoutSuccessUrl("/admin/login");
            }

            @Override
            public void configure(WebSecurity web) throws Exception {
                web
                        .ignoring()
                        .antMatchers(
                                HttpMethod.GET,
                                "/",
                                "/*.html",
                                "/**/favicon.ico",
                                "/**/*.html",
                                "/**/*.css",
                                "/**/*.js",
                                "/**/*.xlsx",
                                "/admin/css/**",
                                "/admin/fonts/**",
                                "/admin/images/**",
                                "/admin/js/**",
                                "/admin/libs/**"

                        )
                ;
            }

            @Override
            public void configure(AuthenticationManagerBuilder authenticationManagerBuilder) throws Exception {
                authenticationManagerBuilder
                        .userDetailsService(userDetailsService)
                        .passwordEncoder(passwordEncoder);
            }
        }
    }

pom

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>0.9.1</version>
    </dependency>
    <dependency>
        <groupId>javax.xml.bind</groupId>
        <artifactId>jaxb-api</artifactId>
    </dependency>

[Multiple Spring Boot Security Configuration](https://medium.com/@igor.bonny/multiple-spring-boot-security-configuration-c876f1b6061e)
