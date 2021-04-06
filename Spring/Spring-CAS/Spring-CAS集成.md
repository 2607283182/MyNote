## 官方文档翻译与理解
CAS 全称 Central Authentication Service，中央鉴权服务。
## 作用
把多个项目搞一个统一登录，以后多个系统登录鉴权，只使用这一套用户名密码即可。
## Spring Security 与 CAS 集成流程
分为3种角色
1. web browser user , web用户
2. CAS server , CAS鉴权服务器
3. Spring Secruity Server SpringSecruity服务器
下面Spring Secruity Server 简称为SS
操作步骤
```
1. The web user is browsing the service’s public pages. CAS or Spring Security is not involved.

web用户访问服务的公共界面,不会涉及到CAS 或者SS调用。
```
```
2.The user eventually requests a page that is either secure or one of the beans it uses is secure. Spring Security’s ExceptionTranslationFilter will detect the AccessDeniedException or AuthenticationException.

用户访问的界面是受保护的界面(需要鉴权的,但是用户还没有鉴权),SS的 "异常过滤器"将捕获 "访问禁止异常" 或 "授权异常"。
```
```
3.Because the user’s Authentication object (or lack thereof) caused an AuthenticationException, the ExceptionTranslationFilter will call the configured AuthenticationEntryPoint. If using CAS, this will be the CasAuthenticationEntryPoint class.

由于上一步的鉴权对象没验证通过，“异常过滤器” 将会调用 “身份验证入口点这个类”，因为当前使用的是CAS 鉴权，所以会调用 “CAS身份验证入口点这个类”。
```
```
4.The CasAuthenticationEntryPoint will redirect the user’s browser to the CAS server. It will also indicate a service parameter, which is the callback URL for the Spring Security service (your application). For example, the URL to which the browser is redirected might be https://my.company.com/cas/login?service=https%3A%2F%2Fserver3.company.com%2Fwebapp%2Flogin/cas.

CAS身份验证入口点这个类会把界面跳转到 “CAS鉴权服务器” 认证界面，并且携带returnUrl。
```
```
5.After the user’s browser redirects to CAS, they will be prompted for their username and password. If the user presents a session cookie which indicates they’ve previously logged on, they will not be prompted to login again (there is an exception to this procedure, which we’ll cover later). CAS will use the PasswordHandler (or AuthenticationHandler if using CAS 3.0) discussed above to decide whether the username and password is valid.

在 “web用户” 界面跳转到 "CAS鉴权服务器界面" 后。“web用户”输入他的账户密码，“CAS鉴权服务器” 验证“web用户“的账号密码是否合法。
```
```
6.Upon successful login, CAS will redirect the user’s browser back to the original service. It will also include a ticket parameter, which is an opaque string representing the "service ticket". Continuing our earlier example, the URL the browser is redirected to might be https://server3.company.com/webapp/login/cas?ticket=ST-0-ER94xMJmn6pha35CQRoZ.

登录成功后，“CAS鉴权服务器将跳转到原服务界面”，它会携带一个ticket参数。
```
```
7.Back in the service web application, the CasAuthenticationFilter is always listening for requests to /login/cas (this is configurable, but we’ll use the defaults in this introduction). The processing filter will construct a UsernamePasswordAuthenticationToken representing the service ticket. The principal will be equal to CasAuthenticationFilter.CAS_STATEFUL_IDENTIFIER, whilst the credentials will be the service ticket opaque value. This authentication request will then be handed to the configured AuthenticationManager.

至此，又回到了"SpringSecruity服务器"，过滤器构造一个被“鉴权管理器”管理的“鉴权token”
```
```
8.The AuthenticationManager implementation will be the ProviderManager, which is in turn configured with the CasAuthenticationProvider. The CasAuthenticationProvider only responds to UsernamePasswordAuthenticationToken s containing the CAS-specific principal (such as CasAuthenticationFilter.CAS_STATEFUL_IDENTIFIER) and CasAuthenticationToken s (discussed later).

这个数据代码处理，可略过。
```
```
9.CasAuthenticationProvider will validate the service ticket using a TicketValidator implementation. This will typically be a Cas20ServiceTicketValidator which is one of the classes included in the CAS client library. In the event the application needs to validate proxy tickets, the Cas20ProxyTicketValidator is used. The TicketValidator makes an HTTPS request to the CAS server in order to validate the service ticket. It may also include a proxy callback URL, which is included in this example: https://my.company.com/cas/proxyValidate?service=https%3A%2F%2Fserver3.company.com%2Fwebapp%2Flogin/cas&ticket=ST-0-ER94xMJmn6pha35CQRoZ&pgtUrl=https://server3.company.com/webapp/login/cas/proxyreceptor.

"CAS鉴权提供器" 将使用“CAS_TICKET验证器”验证传过来的ticket，也就是再去请求“CAS鉴权服务器” 并带着ticket和之前的returnUrl还有这次需要被回调的接口。
```
```
10.Back on the CAS server, the validation request will be received. If the presented service ticket matches the service URL the ticket was issued to, CAS will provide an affirmative response in XML indicating the username. If any proxy was involved in the authentication (discussed below), the list of proxies is also included in the XML response.

“CAS鉴权服务器”验证通过后，返回验证结果给"SS",
```
```
11.[OPTIONAL] If the request to the CAS validation service included the proxy callback URL (in the pgtUrl parameter), CAS will include a pgtIou string in the XML response. This pgtIou represents a proxy-granting ticket IOU. The CAS server will then create its own HTTPS connection back to the pgtUrl. This is to mutually authenticate the CAS server and the claimed service URL. The HTTPS connection will be used to send a proxy granting ticket to the original web application. For example, https://server3.company.com/webapp/login/cas/proxyreceptor?pgtIou=PGTIOU-0-R0zlgrl4pdAQwBvJWO3vnNpevwqStbSGcq3vKB2SqSFFRnjPHt&pgtId=PGT-1-si9YkkHLrtACBo64rmsi3v2nf7cpCResXg5MpESZFArbaZiOKH.

[可选的]
个人理解：如果9步加了这次的回调URL，则回调URL发送鉴权信息，也就是异步，如果不加的话，就是同步。
```
```
12.The Cas20TicketValidator will parse the XML received from the CAS server. It will return to the CasAuthenticationProvider a TicketResponse, which includes the username (mandatory), proxy list (if any were involved), and proxy-granting ticket IOU (if the proxy callback was requested).
"SS"收到了“CAS鉴权服务器” respones后，用 “CAS转换TICKECT验证器”转换成统一格式的response,然后返回一个ticketResponse
```
```
13.Next CasAuthenticationProvider will call a configured CasProxyDecider. The CasProxyDecider indicates whether the proxy list in the TicketResponse is acceptable to the service. Several implementations are provided with Spring Security: RejectProxyTickets, AcceptAnyCasProxy and NamedCasProxyDecider. These names are largely self-explanatory, except NamedCasProxyDecider which allows a List of trusted proxies to be provided.

验证是否发送该ticket是否被“SS”信任。
```
```
14.CasAuthenticationProvider will next request a AuthenticationUserDetailsService to load the GrantedAuthority objects that apply to the user contained in the Assertion.

If there were no problems, CasAuthenticationProvider constructs a CasAuthenticationToken including the details contained in the TicketResponse and the GrantedAuthoritys.

Control then returns to CasAuthenticationFilter, which places the created CasAuthenticationToken in the security context.

The user’s browser is redirected to the original page that caused the AuthenticationException (or a custom destination depending on the configuration).
最后返回鉴权是否成功。
```