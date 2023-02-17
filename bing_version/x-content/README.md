# X-Content-Type-Options HTTP Security Header

The X-Content-Type-Options HTTP security header is a marker used by the server to indicate that the MIME types advertised in the Content-Type headers should not be changed and be followed. This is a way to opt out of MIME type sniffing, or, in other words, to say that the MIME types are deliberately configured¹².

## Recommended Value

The recommended value for this header is `nosniff`. This value informs the browser that it must not perform MIME type sniffing and instead rely on the explicitly declared value[^10^]. For example:

```http
X-Content-Type-Options: nosniff
```

## Non-recommended Values

There are no other valid values for this header besides `nosniff`. If this header is omitted or set to any other value, browsers may perform MIME type sniffing, which can transform non-executable MIME types into executable MIME types (MIME Confusion Attacks)³¹⁵. This can lead to cross-site scripting (XSS) vulnerabilities if malicious content is uploaded by users and served with an incorrect MIME type².

## Tips for Developers

Developers should be aware of some tips when adding this header:

- This header only applies to resources such as scripts, stylesheets, images, etc. It does not affect HTML documents¹.
- This header was introduced in Internet Explorer 8 and is supported by most modern browsers. However, some older browsers may ignore it and still perform MIME type sniffing²¹⁴.
- This header should be used in conjunction with proper Content-Type headers and file extensions for all resources. It does not replace them but rather reinforces them³.
- Developers should test their websites with different browsers and tools such as curl or Fiddler to ensure that this header is correctly implemented and no MIME type sniffing occurs[^10^].

## Sample Configurations

Here are some sample configurations for popular webserver technologies:

### IIS

To enable this header on IIS, you need to add a custom HTTP response header in your web.config file. For example:

```xml
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Content-Type-Options" value="nosniff" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

Alternatively, you can use URL Rewrite Module to add this header dynamically based on conditions⁴.

### Tomcat

To enable this header on Tomcat, you need to add a filter in your web.xml file. For example:

```xml
<filter>
  <filter-name>httpHeaderSecurity</filter-name>
  <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
  <init-param>
    <param-name>xssProtectionEnabled</param-name>
    <param-value>true</param-value>
  </init-param>
</filter>

<filter-mapping>
  <filter-name>httpHeaderSecurity</filter-name>
  <url-pattern>/*</url-pattern>
  <dispatcher>REQUEST</dispatcher>
</filter-mapping>

```

This filter will also add other security headers such as X-XSS-Protection and X-Frame-Options⁵.

### Apache

To enable this header on Apache, you need to use mod_headers module and add a directive in your .htaccess file or your virtual host configuration file. For example:

```apache
Header always set X-Content-Type-Options "nosniff"
```

You can also use mod_rewrite module to add this header conditionally based on rules⁶.

### Nginx

To enable this header on Nginx, you need to add a directive in your nginx.conf file or your server block file. For example:

```nginx
add_header X-Content-Type-Options "nosniff";
```

You can also use ngx_http_rewrite_module module to add this header dynamically based on variables⁷.
