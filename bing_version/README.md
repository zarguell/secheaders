# X-XSS-Protection HTTP Security Header

The X-XSS-Protection HTTP security header is a feature of some browsers (such as Internet Explorer, Chrome and Safari) that stops pages from loading when they detect reflected cross-site scripting (XSS) attacks²³. XSS attacks are a type of web application vulnerability where malicious scripts are injected into trusted web pages, usually by exploiting user input³⁴.

## Recommended Value

The recommended value for the X-XSS-Protection header is `1; mode=block`, which will enable the XSS protection and instruct the browser to block the response in the event that a malicious script has been inserted from user input instead of sanitizing⁵⁶.

## Non-Recommended Values and Dangers

Some non-recommended values for the X-XSS-Protection header are:

- `0`: This value disables the XSS protection, which is not advisable unless you have a strong Content-Security-Policy that disables the use of inline JavaScript ('unsafe-inline')²⁶.
- `1`: This value enables the XSS protection, but instead of blocking the response, it will sanitize the page by removing or modifying the malicious script. This may cause some unintended side effects or break some functionality on your web page⁵⁶.
- `1; report=<reporting-uri>`: This value enables the XSS protection and instructs the browser to send a report of any XSS attack to a specified URL. However, this feature is only supported by Chrome and it may leak sensitive information to third parties².

## Tips for Developers

When adding the X-XSS-Protection header to your web application, here are some tips for developers to be aware of:

- The X-XSS-Protection header only works against reflected XSS attacks, not stored or DOM-based XSS attacks. Therefore, you should still implement proper input validation and output encoding on your server-side and client-side code²³.
- The X-XSS-Protection header is becoming unnecessary with increasing content-security-policy of sites. Modern browsers support more powerful and flexible mechanisms to prevent XSS attacks, such as Content-Security-Policy (CSP) and Subresource Integrity (SRI)[^2 ^] [ ^6 ^ ] [ ^7 ^ ].
- The X-XSS-Protection header may introduce vulnerabilities in some cases, such as when it interacts with other headers or scripts on your web page. For example, if you set `X-XSS-Protection: 0` on one page but not on another page that includes it via an iframe, you may expose yourself to clickjacking attacks[^6 ^ ] [ ^7 ^ ]. Therefore, you should test your web application thoroughly after adding or changing this header.

## Sample Configurations

To enable the X-XSS-Protection header on your web server, you need to add a configuration directive depending on your web server technology. Here are some sample configurations for popular web servers:

### IIS
To enable the X-XSS-Protection header on IIS, you need to add a custom HTTP response header in your web.config file. For example:

```xml
<system.webServer>
    <httpProtocol>
        <customHeaders>
            <add name="X-XSS-Protection" value="1; mode=block" />
        </customHeaders>
    </httpProtocol>
</system.webServer>
```

### Tomcat
To enable the X-XSS-Protection header on Tomcat, you need to add a filter in your web.xml file. For example:

```xml
<filter>
    <filter-name>httpHeaderSecurity</filter-name>
    <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
    <init-param>
        <param-name>xssProtectionEnabled</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>xssProtectionBlock</param-name>
        <param-value>true</param-value>
    </init-param>    
</filter>

<filter-mapping> 
    <filter-name>httpHeaderSecurity</filter-name> 
    <url-pattern>/*</url-pattern> 
    <dispatcher>REQUEST</dispatcher> 
</filter-mapping>

```

### Apache
To enable the X-XSS-Protection header on Apache, you need to add a directive in your .htaccess file or httpd.conf file. For example:

```apacheconf
Header always set X-XSS-Protection "1; mode=block"
```
### Nginx
To enable the X-XSS-Protection header on Nginx, you need to add a directive in your nginx.conf file or your server block file. For example:

```nginx
add_header X-XSS-Protection "1; mode=block";
```

Next, restart the Nginx service to apply the changes¹³.
