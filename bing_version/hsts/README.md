# Strict-Transport-Security (HSTS) HTTP Security Header

## What is HSTS?

HSTS stands for HTTP Strict Transport Security and was specified by the IETF in RFC 6797 back in 2012¹. It was created as a way to force the browser to use secure connections when a site is running over HTTPS. It is a security header that you add to your web server and is reflected in the response header as `Strict-Transport-Security`¹.

HSTS informs browsers that the site should only be accessed using HTTPS, and that any future attempts to access it using HTTP should automatically be converted to HTTPS requests². This can help prevent man-in-the-middle attacks, such as SSL stripping, where an attacker intercepts an HTTP request and redirects it to a malicious site³.

## What is the recommended value for HSTS?

The recommended value for HSTS depends on your site's needs, but generally you want to set a high `max-age` value, include all subdomains, and indicate a willingness to be preloaded into browsers.

The `max-age` directive specifies how long (in seconds) browsers should remember that your site is only accessible via HTTPS. A high value, such as 31536000 (12 months) or 63072000 (24 months), ensures that browsers will not try to access your site via HTTP for a long time⁴⁵.

The `includeSubDomains` directive tells browsers that all present and future subdomains of your site should also be accessed via HTTPS. This can prevent attackers from exploiting insecure subdomains that may not have HTTPS enabled²⁶.

The `preload` directive indicates that you want your domain to be included in the HSTS preload list maintained by Chrome (and used by Firefox and Safari). This list contains domains that are hardcoded into browsers as HSTS-enabled, so they never need to be accessed via HTTP even for the first time. This can provide extra protection against attacks that target the initial connection before HSTS takes effect²⁷.

In its strongest and recommended form, the HSTS policy includes all subdomains, and indicates a willingness to be preloaded into browsers:

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

## What are some non-recommended values for HSTS and why are they dangerous?

Some non-recommended values for HSTS are:

- A low or zero `max-age` value. This means that browsers will forget about your HSTS policy soon or immediately, leaving your site vulnerable to HTTP requests again⁸.
- Omitting the `includeSubDomains` directive. This means that only your main domain will be protected by HSTS, while any subdomains may still be accessed via HTTP if they are not explicitly configured with HTTPS⁶.
- Omitting the `preload` directive. This means that your domain will not be added to the preload list, which means that browsers will still need to access your site via HTTP at least once before they learn about your HSTS policy. This creates a window of opportunity for attackers to intercept or modify the initial connection⁷.

These values can weaken or negate the benefits of HSTS, so they should be avoided unless you have a specific reason not to use them.

## What are some tips for developers when adding HSTS?

Some tips for developers when adding HSTS are:

- Test your site with HTTPS before enabling HSTS. Make sure that all resources (images, scripts, stylesheets, etc.) are loaded over HTTPS as well, otherwise you may encounter mixed content errors or broken functionality⁹.
- Use tools like SSL Labs or Security Headers to check your site's SSL/TLS configuration and security headers. These tools can help you identify any issues or vulnerabilities with your encryption or headers settings[^10^] ¹¹.
- Be careful when using wildcard certificates or multiple domains on one server. If you enable HSTS on one domain but not on another, you may create conflicts or inconsistencies with how browsers handle HTTPS requests across different domains sharing the same certificate or server¹².
- Be aware of the implications of preloading. Once you submit your domain for preloading, it may take several weeks or months before it is accepted and added to browsers' lists. After that point, it will be very difficult or impossible to remove it from the list if you change your mind or encounter problems with HTTPS on your site. Therefore, you should only use preloading if you are confident that you can support HTTPS on your domain indefinitely[^7^

## How to configure HSTS on different web servers?

The exact steps to configure HSTS on different web servers may vary depending on your server version and settings, but here are some general guidelines and examples for some popular web server technologies:

### IIS

To enable HSTS on IIS, you need to install the URL Rewrite module and create a custom outbound rule that adds the `Strict-Transport-Security` header to HTTPS responses. You can use the IIS Manager GUI or edit the web.config file directly. Here is an example of a web.config file with a rule that sets HSTS with a max-age of one year, includes subdomains, and enables preloading:

```xml
<configuration>
  <system.webServer>
    <rewrite>
      <outboundRules>
        <rule name="Add Strict-Transport-Security when HTTPS" enabled="true">
          <match serverVariable="RESPONSE_Strict_Transport_Security" pattern=".*" />
          <conditions>
            <add input="{HTTPS}" pattern="on" ignoreCase="true" />
          </conditions>
          <action type="Rewrite" value="max-age=31536000; includeSubDomains; preload" />
        </rule>
      </outboundRules>
    </rewrite>
  </system.webServer>
</configuration>
For more information, see this guide on how to use the URL Rewrite module.
```

### Tomcat

To enable HSTS on Tomcat, you need to add a security-constraint element to your web.xml file that specifies HTTPS as the transport guarantee for your site. You also need to add a header element to your server.xml file that adds the Strict-Transport-Security header to HTTPS responses. Here is an example of a web.xml file with a security constraint that forces HTTPS for all requests:

```
<web-app ...>

  ...

  <security-constraint>
    <web-resource-collection>
      <web-resource-name>Entire Application</web-resource-name>
      <url-pattern>/*</url-pattern>
    </web-resource-collection>

    <!-- user-data-constraint specifies how data communicated between client and server should be protected -->
    <!-- CONFIDENTIAL means it must be protected by encryption (HTTPS) -->
    <!-- NONE means it can be unprotected (HTTP) -->
    <!-- INTEGRAL means it must be protected by data integrity mechanisms (such as digital signatures) -->
    <user-data-constraint>
      <transport-guarantee>CONFIDENTIAL</transport-guarantee> 
    </user-data-constraint>

  </security-constraint>

  ...

</web-app>
```

Here is an example of a server.xml file with a header element that sets HSTS with a max-age of one year, includes subdomains, and enables preloading:

```
<Server ...>

  ...

  <!-- Define an HTTP/1.1 Connector on port 8443 -->
  <!-- This connector uses JSSE configuration -->
  <!-- When using APR/native library, use OpenSSL style configuration -->

  <!-- For more information see https://tomcat.apache.org/tomcat-9.0-doc/config/http.html#SSL_Support -->

  <Connector port="8443"
             protocol="org.apache.coyote.http11.Http11NioProtocol"
             maxThreads="150"
             SSLEnabled="true">

     ...

     <!-- Add Strict-Transport-Security header -->
     <!-- For more information see https://tomcat.apache.org/tomcat-9.0-doc/config/http.html#Standard_Implementation -->

     <header name="Strict-Transport-Security" value="max-age=31536000; includeSubDomains; preload"/>

     ...

   </Connector>

   ...

</Server>
```

For more information, see this guide on how to configure SSL/TLS support for Tomcat.

### Apache
To enable HSTS on Apache, you need to enable the mod_headers module and add a directive that adds the Strict-Transport-Security header to HTTPS responses. You can use either .htaccess files or virtual host configurations depending on your preference and setup. Here is an example of an .htaccess file with a directive that sets HSTS with a max-age of one year, includes subdomains, and enables preloading:

```
# Enable mod_headers
<IfModule mod_headers.c>

# Add Strict-Transport-Security header
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
```
