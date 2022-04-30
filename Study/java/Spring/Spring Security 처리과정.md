# Spring Security처리과정
## __<span style="color:#9999ff">Spring Security 처리과정</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FUOabX%2FbtqEJBBNixH%2FPGDv64FTKaBSLzMiiXkA3K%2Fimg.png)
1. __<span style="color:#ff9933">사전 세팅</span>__
   - 정적 자원을 제공하는 클래스를 생성하여 아래와 같이 설정한다.
   - ``` java
        @Configuration 
        public class WebMvcConfig implements WebMvcConfigurer { 
            private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/static/", "classpath:/public/", "classpath:/", "classpath:/resources/", "classpath:/META-INF/resources/", "classpath:/META-INF/resources/webjars/"
            }; 
            @Override 
            public void addViewControllers(ViewControllerRegistry registry) { 
                // /에 해당하는 url mapping을 /common/test로 forward한다. 
                registry.addViewController( "/" ).setViewName( "forward:/index" ); 
                // 우선순위를 가장 높게 잡는다. 
                registry.setOrder(Ordered.HIGHEST_PRECEDENCE); 
            } 
            @Override
            public void addResourceHandlers(ResourceHandlerRegistry registry) { 
                registry.addResourceHandler("/**").addResourceLocations(CLASSPATH_RESOURCE_LOCATIONS); 
            } 
        }
   - 그리고 SpringSecurity에 대한 기본적인 설정들을 추가한다.SpringSecurity에 대한 설정 클래스에서는
     - configure 메소드를 통해 정적 자원들에 대해서는 Security를 적용하지 않음을 추가한다.
     - configure 메소드를 통해 어떤 요청에 대해서는 로그인을 요구하고, 어떤 요청에 대해서 로그인을 요구하지 않을지 설정한다.
     - form 기반의 로그인을 활용하는 경우 로그인 URL, 로그인 성공시 전달할 URL, 로그인 실패시 URL등에 대해서 설정한다.
   - ``` java
        @Configuration 
        @EnableWebSecurity 
        public class WebSecurityConfig extends WebSecurityConfigurerAdapter { 
            // 정적 자원에 대해서는 Security 설정을 적용하지 않음. 
            @Override 
            public void configure(WebSecurity web) { 
                web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
            } 
            @Override 
            protected void configure(HttpSecurity http) throws Exception { 
                http.csrf().disable().authorizeRequests() 
                // /about 요청에 대해서는 로그인을 요구함 
                .antMatchers("/about").authenticated() 
                // /admin 요청에 대해서는 ROLE_ADMIN 역할을 가지고 있어야 함 
                .antMatchers("/admin").hasRole("ADMIN") 
                // 나머지 요청에 대해서는 로그인을 요구하지 않음
                .anyRequest().permitAll() .and() 
                // 로그인하는 경우에 대해 설정함 
                .formLogin() 
                // 로그인 페이지를 제공하는 URL을 설정함 
                .loginPage("/user/loginView") 
                // 로그인 성공 URL을 설정함 
                .successForwardUrl("/index") 
                // 로그인 실패 URL을 설정함 .
                .failureForwardUrl("/index") 
                .permitAll() 
                .and() 
                .addFilterBefore(customAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
            }
            @Bean 
            public BCryptPasswordEncoder bCryptPasswordEncoder() { 
                return new BCryptPasswordEncoder(); 
            } 
        }
   - 이번 예제에서는 /about에 대해서는 로근인이 성공한 경우에만 접근가능하고 /admin에 대해서는 로그인 후에 ROLE_ADMIN역할을 갖고 있는 경우에만 접근가능하도록 설정해준다.
2. __<span style="color:#ff9933">로그인 요청</span>__
   - 사용자는 로그인 하기 위해 아이디와 비밀번호를 입력해서 로그인 요청을 하게 된다. 이번에 작성하는 예제에서는 Form기반으로 요청을 보내는 상황이다.
3. __<span style="color:#ff9933">UserPasswordAuthenticationToken</span>__
   - 전송이 오면 AuthenticationFilter로 요청이 먼저오게 되고 아이디와 비밀번호를 기반으로 UserPasswordAuthenticationToken을 발급해주어야 한다.
   - ``` java
        @Log4j2 
        public class CustomAuthenticationFilter extends UsernamePasswordAuthenticationFilter { 
            public CustomAuthenticationFilter(AuthenticationManager authenticationManager) { 
                super.setAuthenticationManager(authenticationManager); 
            } 
            @Override 
            public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException { 
                UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(request.getParameter("userEmail"), request.getParameter("userPw")); 
                setDetails(request, authRequest); 
                return this.getAuthenticationManager().authenticate(authRequest);
            }
        }
    - 이렇게 직접 제작한 Filter를 이제 적용시켜야 하므로 UsernamePasswordAuthenticationFilter필터 이전에 적용시켜야 한다. 그리고 해당 CustomAuthenticationFilter가 수행된 후에 처리될 Handler역시 Bean으로 등록하고 CustomAuthenticationFilter의 핸들러로 추가해주어야 하는데 해당 코드들은 WebSecurityConfig에 아래와 같이 추가해줄 수 있다.
    - ``` java
        public class CustomLoginSuccessHandler extends SavedRequestAwareAuthenticationSuccessHandler {
            @Override
            public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response,Authentication authentication) throws IOException { 
                SecurityContextHolder.getContext().setAuthentication(authentication); 
                response.sendRedirect("/about"); 
               }
        }
   - CustomLoginSuccesshanler는 AuthenticationProvider를 통해 인증이 성공될 경우 처리되는데 이번 예제에서는 /about요청으로 redirect되도록 해주었다. 그리고 토큰을 사용하지 않고 세션을 활용하는 지금 예제같은 경우에는 성공하여 반환된 Authentication 객체를 SecurityContextHolder의 Context에 저장해주어야 한다. 나중에 사용자의 정보를 꺼낼 경우에도 SecurityContextHolder의 Context에서 조회하면 된다.
   - ``` java
        @Configuration 
        @EnableWebSecurity 
        public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
            // 정적 자원에 대해서는 Security 설정을 적용하지 않음. 
            @Override 
            public void configure(WebSecurity web) { 
                web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations()); 
            } 
            @Override 
            protected void configure(HttpSecurity http) throws Exception { 
                http.csrf().disable().authorizeRequests() 
                // /about 요청에 대해서는 로그인을 요구함 
                .antMatchers("/about").authenticated() 
                // /admin 요청에 대해서는 ROLE_ADMIN 역할을 가지고 있어야 함 
                .antMatchers("/admin").hasRole("ADMIN") 
                // 나머지 요청에 대해서는 로그인을 요구하지 않음 
                .anyRequest().permitAll() 
                .and() 
                // 로그인하는 경우에 대해 설정함 
                .formLogin() 
                // 로그인 페이지를 제공하는 URL을 설정함 
                .loginPage("/user/loginView") 
                // 로그인 성공 URL을 설정함 
                .successForwardUrl("/index") 
                // 로그인 실패 URL을 설정함 
                .failureForwardUrl("/index") 
                .permitAll() 
                .and() 
                .addFilterBefore(customAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class); 
            } 
            @Bean 
            public BCryptPasswordEncoder bCryptPasswordEncoder() { 
                return new BCryptPasswordEncoder(); 
            } 
            @Bean 
            public CustomAuthenticationFilter customAuthenticationFilter() throws Exception {
                CustomAuthenticationFilter customAuthenticationFilter = new CustomAuthenticationFilter(authenticationManager()); customAuthenticationFilter.setFilterProcessesUrl("/user/login");
                customAuthenticationFilter.setAuthenticationSuccessHandler(customLoginSuccessHandler());
                customAuthenticationFilter.afterPropertiesSet(); return customAuthenticationFilter; 
            } 
            @Bean 
            public CustomLoginSuccessHandler customLoginSuccessHandler() { 
                return new CustomLoginSuccessHandler(); 
            } 
        }
   - CustomAuthenticationFilter를 빈으로 등록하는 과정에서 UserName파라미터와 UserPassword파라미터를 설정할 수 있다. 이러한 과정을 거치면 UsernamePasswordToken이 발급된다.
4. __<span style="color:#ff9933">UsernamePasswordToken을 Authentication Manager에게 전달</span>__
   - AuthenticationFilter는 생성한 UsernamePasswordToken을 authenticationManager에게 전달한다. AuthenticationManager은 실제로 인증을 처리할 여려개의 AuthenticationProvider를 가지고 있다.
5. __<span style="color:#ff9933">UsernamePasswordToken을 Authentication provider에게 전달</span>__
   - AuthenticationManager는 전달받는 UsernamePasswordToken을 순차적으로 AuthenticationProvider들에게 전달하여 실제 인증의 과정을 수행해야 하며, 실제 인증에 대한 부분은 authenticate함수에 작성을 해주어야 한다. SpringSecurity에서는 Username으로 DB에서 데이터를 조회한 다음에 비밀번호의 일치 여부를 검사하는 방식으로 작동을 한다. 그렇기 때문에 먼저 UsernamePasswordToken 토큰으로부터 아이디를 조회해야 하고 그 코드는 아래와 같다.
   - ``` java
        @RequiredArgsConstructor 
        @Log4j2 
        public class CustomAuthenticationProvider implements AuthenticationProvider { 
            @Override 
            public Authentication authenticate(Authentication authentication) throws AuthenticationException { 
                UsernamePasswordAuthenticationToken token = (UsernamePasswordAuthenticationToken) authentication; 
                // AuthenticaionFilter에서 생성된 토큰으로부터 아이디와 비밀번호를 조회함 
                String userEmail = token.getName(); 
            } 
            @Override 
            public boolean supports(Class<?> authentication) { 
                return authentication.equals(UsernamePasswordAuthenticationToken.class); 
            } 
        }
6. __<span style="color:#ff9933">UserDetailsService로 조회할 아이디를 전달</span>__
   - AuthenticationProvider에서 아이디를 조회하였으면 UserDetailsService로부터 아이디를 기반으로 데이터를 조회해야한다. UserDetailsService는 인터페이스이기 때문에 이를 implements한 클래스를 작성해주어야 한다. 실제 반환값을 작성하는 부분은 8번에서 다룬다.
   - ``` java 
        @RequiredArgsConstructor 
        @Service 
        public class UserDetailsServiceImpl implements UserDetailsService { 
            private final UserRepository userRepository; 
            @Override 
            public UserDetailsVO loadUserByUsername(String userEmail) { 

            } 
        }
7. __<span style="color:#ff9933">아이디를 기반으로 DB에서 데이터 조회</span>__
   - 전달받은 아이디를 기반으로 DB에서 조회하는 구현체는 우리가 직접 개발한 UserVO일 것이고, UserDetailsService의 반환값은 UserDetails 인터페이스이기 때문에 이를 implements하여 구현한 UserDetailsVO를 아래와 같이 작성할 수 있다.
   - ``` java
        @RequiredArgsConstructor 
        @Getter 
        public class UserDetailsVO implements UserDetails { 
            @Delegate 
            private final UserVO userVO; 
            private final Collection<? extends GrantedAuthority> authorities;
            @Override 
            public Collection<? extends GrantedAuthority> getAuthorities() { 
                return authorities; 
            } 
            @Override 
            public String getPassword() { 
                return userVO.getUserPw(); 
            } 
            @Override 
            public String getUsername() { 
                return userVO.getUserEmail(); 
            } 
            @Override 
            public boolean isAccountNonExpired() { 
                return userVO.getIsEnable(); 
            } 
            @Override 
            public boolean isAccountNonLocked() { 
                return userVO.getIsEnable(); 
            } 
            @Override 
            public boolean isCredentialsNonExpired() { 
                return userVO.getIsEnable(); 
            } 
            @Override 
            public boolean isEnabled() { 
                return userVO.getIsEnable(); 
            }
        }
8. __<span style="color:#ff9933">아이디를 기반으로 조회한 결과를 반환</span>__
   - 아이디를 기반으로 조회한 결과를 반환하기 위해서는 위에서 작성하던 UserDetailsServiceImpl을 마무리해주어야 하는데, 그 전에 사용자의 아이디를 기반으로 데이터가 조회하지 않았을 경우 처리해주기 외한 Execption 클래스를 추가해주어야 한다.
   - ``` java
        public class UserNotFoundException extends RuntimeException{ 
            public UserNotFoundException(String userEmail){ 
                super(userEmail + " NotFoundException"); 
            } 
        }
   - 그리고 조회한 결과를 CustomAuthenticationProvider로 반환하는 UserDetailsServiceImpl을 마무리해주면 아래와같다.
   - ``` java 
        @RequiredArgsConstructor 
        @Service 
        public class UserDetailsServiceImpl implements UserDetailsService { 
            private final UserRepository userRepository; 
            @Override
            public UserDetailsVO loadUserByUsername(String userEmail) { 
                return userRepository
                .findByUserEmail(userEmail)
                .map(u -> new UserDetailsVO(u, Collections.singleton(new SimpleGrantedAuthority(u.getRole().getValue()))))
                .orElseThrow(() -> new UserNotFoundException(userEmail)); 
                } 
        }
    - 위의 예제에서는 UserRepository로부터 조회한 결과를 Optional로 반환하고 있기 때문에 Map 함수를 이용해서 새로운 UserDetailsVO 객체로 생성하여 반환하고 있다.
9. __<span style="color:#ff9933">인증 처리 후 인증된 토큰을 AuthenticationManager에게 반환</span>__
   - 이제 CustomAuthenticationProviderdpt UserDetailsService를 통해 조회한 정보와 입력받은 비밀번호가 일치하는지 확인하여 일치한다면 인증된 토큰을생성하여 반환해주어야 한다. DB에 저장된 사용자 비밀번호는 암호화가 되어있기 때문에, 입력으로부터 들어온 비밀번호를 PasswordEncoder를 통해 암호화하여 DB에서 조회한 사용자의 비밀번호와 조회한 사용자의 비밀번호화 매칭 되는지 확인해주어야 한다. 만약 비밀번호가 매칭되지 않는 경우에는 BadCredentialsException을 발생시켜 처리해준다.
   - ``` java
        @RequiredArgsConstructor 
        @Log4j2 
        public class CustomAuthenticationProvider implements AuthenticationProvider { 
            private final UserDetailsService userDetailsService; 
            private final BCryptPasswordEncoder passwordEncoder; 
            @Override 
            public Authentication authenticate(Authentication authentication) 
            throws AuthenticationException 
            { 
                UsernamePasswordAuthenticationToken token = 
                (UsernamePasswordAuthenticationToken) authentication; 
                // AuthenticaionFilter에서 생성된 토큰으로부터 아이디와 비밀번호를 조회함 
                String userEmail = token.getName(); 
                String userPw = (String) token.getCredentials(); 
                // UserDetailsService를 통해 DB에서 아이디로 사용자 조회 
                UserDetailsVO userDetailsVO = 
                (UserDetailsVO) userDetailsService.loadUserByUsername(userEmail); 
                if (!passwordEncoder.matches(userPw, userDetailsVO.getPassword())) { 
                    throw new BadCredentialsException(userDetailsVO.getUsername() + "Invalid password"); 
                } 
                return new UsernamePasswordAuthenticationToken(userDetailsVO, userPw, userDetailsVO.getAuthorities()); 
            } 
            @Override public boolean supports(Class<?> authentication) { 
                return authentication.equals(UsernamePasswordAuthenticationToken.class); 
            } 
        }
   - 위와 같이 완선된 CustomAuthenticationProvider를 이제 Bean으로 등록해주어야 하는데 이것을 WebSecurityConfig작성하면 아래와 같다.
   - ``` java
        @Configuration 
        @EnableWebSecurity 
        @RequiredArgsConstructor 
        public class WebSecurityConfig extends WebSecurityConfigurerAdapter { 
            private final UserDetailsService userDetailsService; 
            // 정적 자원에 대해서는 Security 설정을 적용하지 않음. 
            @Override 
            public void configure(WebSecurity web) { 
                web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations()); 
            } 
            @Override 
            protected void configure(HttpSecurity http) throws Exception { 
                http.csrf().disable().authorizeRequests() 
                // /about 요청에 대해서는 로그인을 요구함 
                .antMatchers("/about").authenticated() 
                // /admin 요청에 대해서는 ROLE_ADMIN 역할을 가지고 있어야 함 
                .antMatchers("/admin").hasRole("ADMIN") 
                // 나머지 요청에 대해서는 로그인을 요구하지 않음 
                .anyRequest().permitAll() 
                .and() 
                // 로그인하는 경우에 대해 설정함 
                .formLogin() 
                // 로그인 페이지를 제공하는 URL을 설정함 
                .loginPage("/user/loginView") 
                // 로그인 성공 URL을 설정함 
                .successForwardUrl("/index") 
                // 로그인 실패 URL을 설정함 
                .failureForwardUrl("/index") 
                .permitAll() 
                .and() 
                .addFilterBefore(customAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class); 
                } 
                @Bean 
                public BCryptPasswordEncoder bCryptPasswordEncoder() { 
                    return new BCryptPasswordEncoder(); 
                } 
                @Bean 
                public CustomAuthenticationFilter customAuthenticationFilter() throws Exception { 
                    CustomAuthenticationFilter customAuthenticationFilter = new CustomAuthenticationFilter(authenticationManager()); 
                    customAuthenticationFilter.setFilterProcessesUrl("/user/login");
                    customAuthenticationFilter.setAuthenticationSuccessHandler(customLoginSuccessHandler());
                    customAuthenticationFilter.afterPropertiesSet(); 
                    return customAuthenticationFilter; 
                } 
                @Bean 
                public CustomLoginSuccessHandler customLoginSuccessHandler() { 
                    return new CustomLoginSuccessHandler(); 
                } 
                @Bean 
                public CustomAuthenticationProvider customAuthenticationProvider() { 
                    return new CustomAuthenticationProvider(bCryptPasswordEncoder()); 
                } 
                @Override 
                public void configure(AuthenticationManagerBuilder authenticationManagerBuilder) 
                {
                    authenticationManagerBuilder.authenticationProvider(customAuthenticationProvider()); 
                } 
        }
10. __<span style="color:#ff9933">인증된 토큰을 AuthenticaionFilter에게 전달</span>__
    - AuthenticationProvider에서 인증이 완료된 UsernamePasswordAuthenticationToken을 AuthenticationFilter로 반환하고 AuthenticaionFilter에서는 Loginsuccesshandler로 전달된다.
11. __<span style="color:#ff9933">인증 토큰을 SecurityContextHolder에 저장</span>__
    - LoginSuccessHandler로 넘어온 authentication객체를 SecurityContextHolder에 저장하면 인증 과정이 끝나게 된다.


## 처리과정 요약
1. 사용자가 아이디 비밀번호로 로그인을 요청함
2. AuthenticaionFilter에서 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager에게 전달
3. AuthenticationManager는 등록된 AuthenticationProvider들을 조회하여 인증을 요구함
4. AuthenticationProvider는 UserDetailsService를 통해 입력받은 아이디에 다한 사용자 정보를 DB에서 조회함
5. 입력받은 비밀번호를 암호화 하여 DB의 비밀번호와 매치오디는 경우 인증이 성공된 UsernameAuthenticationToken을 생성하여 AuthenticationManager로 반환함
6. AuthenticationManager는 UsernameAuthenticationToken을 AuthenticationFilter로 전달함
7. AuthenticationFilter는 UsernameAuthenticationToken을 Loginsuccesshandler로 전송하고 SecurityContextHolder에 저장함

# 참고
- https://mangkyu.tistory.com/77