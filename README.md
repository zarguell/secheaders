# HTTP Security Headers
HTTP security headers are a key component in enhancing the security of your website or application. These headers are added to the HTTP response sent from your server and help to mitigate various security risks, such as cross-site scripting (XSS), clickjacking, and more.

In this article, we'll take a look at some of the most important HTTP security headers and provide a recommended value, tips for testing, and sample configurations for popular web server technologies.

## X-XSS-Protection

The X-XSS-Protection header is used to enable the built-in XSS protection offered by some browsers. This header tells the browser to attempt to block detected XSS attacks.

Recommended Value: 
```
X-XSS-Protection: 1; mode=block
```

Testing Tips:
- Test your site with known XSS payloads to ensure that the header is correctly blocking the execution of malicious content.
- Check the browser's developer tools for any XSS protection warnings.

Sample Configurations:

**IIS**
```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-XSS-Protection" value="1; mode=block" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

**Apache**
```
Header set X-XSS-Protection "1; mode=block"
```

**Nginx**
```
add_header X-XSS-Protection "1; mode=block";
```

**Traefik**
```
[http.middlewares.security-headers.headers]
X-XSS-Protection = "1; mode=block"
```

## X-Content-Type-Options

The X-Content-Type-Options header is used to prevent browsers from interpreting files as a different MIME type. This can help protect against certain types of attacks, such as XSS and data injection.

Recommended Value: 
```
X-Content-Type-Options: nosniff
```

Testing Tips:
- Check the browser's developer tools to verify that the header is being sent and that it has the correct value.
- Try to load a file with an incorrect MIME type and verify that the browser does not execute it.

Sample Configurations:

**IIS**
```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Content-Type-Options" value="nosniff" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

**Apache**
```
Header set X-Content-Type-Options "nosniff"
```

**Tomcat**
```
<filter>
  <filter-name>httpHeaderSecurity</filter-name>
  <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
  <async-supported>true</async-supported>
  <init-param>
    <param-name>antiClickJackingOption</param-name>
    <param-value>DENY</param-value>
  </init-param>
  <init-param>
    <param-name>xContentTypeOptions</param-name>
    <param-value>nosniff</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>httpHeaderSecurity</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

**Nginx**
```
add_header X-Content-Type-Options "nosniff";
```

**Traefik**
```
[http.middlewares.security-headers.headers]
X-Content-Type-Options = "nosniff"
```


## Strict-Transport-Security (HSTS)

The Strict-Transport-Security header is used to tell browsers that the site should only be accessed over HTTPS. This helps to prevent SSL stripping attacks, in which an attacker intercepts an insecure connection and downgrades it to an unencrypted connection.

Recommended Value: 
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

Testing Tips:
- Check the browser's developer tools to verify that the header is being sent and that it has the correct value.
- Try to access the site over HTTP and verify that the browser redirects to the HTTPS version of the site.

Sample Configurations:

**IIS**
```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="Strict-Transport-Security" value="max-age=31536000; includeSubDomains" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

**Apache**
```
Header set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

**Tomcat**
```
<filter>
  <filter-name>httpHeaderSecurity</filter-name>
  <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
  <async-supported>true</async-supported>
  <init-param>
    <param-name>hstsMaxAgeSeconds</param-name>
    <param-value>31536000</param-value>
  </init-param>
  <init-param>
    <param-name>hstsIncludeSubDomains</param-name>
    <param-value>true</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>httpHeaderSecurity</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

**Nginx**
```
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
```

**Traefik**
```
[http.middlewares.security-headers.headers]
Strict-Transport-Security = "max-age=31536000; includeSubDomains"
```


## X-Frame-Options

The X-Frame-Options header is used to prevent a page from being displayed in a frame, iframe, or object. This helps to prevent clickjacking attacks, in which an attacker creates a hidden frame on their site that displays a target site, and tricks the user into clicking on a link or button that performs an unintended action.

Recommended Value: 
```
X-Frame-Options: DENY
```

Testing Tips:
- Check the browser's developer tools to verify that the header is being sent and that it has the correct value.
- Try to embed the page in a frame on another site and verify that it is not displayed.

Sample Configurations:

**IIS**
```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Frame-Options" value="DENY" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

**Apache**
```
Header set X-Frame-Options "DENY"
```

**Tomcat**
```
<filter>
  <filter-name>httpHeaderSecurity</filter-name>
  <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
  <async-supported>true</async-supported>
  <init-param>
    <param-name>xssProtectionEnabled</param-name>
    <param-value>true</param-value>
  </init-param>
  <init-param>
    <param-name>xframeOptionsValue</param-name>
    <param-value>DENY</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>httpHeaderSecurity</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

**Nginx**
```
add_header X-Frame-Options "DENY";
```

**Traefik**
```
[http.middlewares.security-headers.headers]
X-Frame-Options = "DENY"
```

## Content-Security-Policy

The Content-Security-Policy header is used to specify the sources of content that are allowed to be loaded by the browser, and to control the types of content that are allowed to execute. This helps to prevent cross-site scripting (XSS) and other code injection attacks, by restricting the types of content that can run in the context of a page, and the sources of that content.

### Directives

The following directives are available for use in a Content-Security-Policy header:

- `default-src`: Specifies the default sources of content for all types.
- `script-src`: Specifies the sources of JavaScript that are allowed to be executed.
- `style-src`: Specifies the sources of CSS that are allowed to be applied to a page.
- `img-src`: Specifies the sources of images that are allowed to be loaded.
- `connect-src`: Specifies the sources of web connections that are allowed to be made (such as XHR, WebSockets, and EventSource connections).
- `font-src`: Specifies the sources of fonts that are allowed to be loaded.
- `frame-src`: Specifies the sources of content that are allowed to be embedded in frames.
- `object-src`: Specifies the sources of plugins that are allowed to be executed (such as Flash or Silverlight).
- `media-src`: Specifies the sources of audio and video files that are allowed to be played.
- `base-uri`: Specifies the sources that can be used as the document base URL.
- `form-action`: Specifies the sources of form submissions that are allowed.
- `frame-ancestors`: Specifies the sources that are allowed to embed the current page in a frame.

### Recommended Value

The recommended value for a Content-Security-Policy header will vary depending on the requirements of your site and the types of content that you serve. A common starting point is to use the following policy:

```
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self'; connect-src 'self'; font-src 'self';
```

This policy allows content from the same origin (the site serving the content) for all types of content. If you need to allow content from other sources, you can add them to the appropriate directive, separated by spaces. For example:

```
Content-Security-Policy: default-src 'self'; script-src 'self' https://example.com; style-src 'self' https://example.com; img-src 'self' https://example.com; connect-src 'self' https://example.com; font-src 'self' https://example.com;
```

This policy allows content from the same origin and from the `https://example.com` source for all types of content.

### Testing Tips

- Always test changes to the CSP header in a development environment before deploying to production
- Use the CSP report-only mode to test and monitor the impact of the policy without breaking your site
- Use a tool such as the Chrome DevTools Security panel to inspect the CSP header and to see any violations that occur
- Consider using a CSP generator tool to help generate the policy, but make sure to understand the generated policy and adjust as needed
- Make sure to test your website's functionality with the CSP policy in place, including form submissions, AJAX requests, and other dynamic content

### Sample configurations for popular webserver technologies

#### Apache
To configure the Content-Security-Policy header in Apache, add the following code in your `.htaccess` file or the Apache configuration file:

```
Header set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';"
```

#### Nginx
To configure the Content-Security-Policy header in Nginx, add the following code in your Nginx configuration file:

```
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';";
```


#### IIS
To configure the Content-Security-Policy header in IIS, follow these steps:

1. Open the Internet Information Services (IIS) Manager.
2. Select the website you want to configure.
3. Double-click on the "HTTP Response Headers" option in the "IIS" section.
4. Click on "Add" in the "Actions" pane.
5. Enter "Content-Security-Policy" as the header name.
6. Enter the desired policy value as the header value.
7. Click "OK".

#### Tomcat
To configure the Content-Security-Policy header in Tomcat, add the following code to your web application's `web.xml` file:

```
<filter>
  <filter-name>CSPFilter</filter-name>
  <filter-class>org.apache.catalina.filters.CspFilter</filter-class>
  <init-param>
    <param-name>csp-policy</param-name>
    <param-value>default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>CSPFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

## Referrer-Policy
The Referrer-Policy header is used to control the behavior of the Referer header, which is sent to a server to indicate the page that linked to the current page. The Referrer-Policy header allows a web page to specify the amount of information that is sent in the Referer header, if any. This can help to reduce the amount of sensitive information that is leaked to other websites, and can also help to improve the privacy of the users of your website.

## Recommended value
The recommended value for Referrer-Policy is `strict-origin-when-cross-origin`. This value ensures that the Referer header is only sent when the current page is being loaded from another page within the same origin (i.e., from a page with the same scheme, hostname, and port). When the current page is loaded from a different origin, the Referer header will not be sent.

## Testing tips
Before deploying the Referrer-Policy header to your production website, it is important to test it thoroughly to ensure that it does not cause any unexpected issues. One of the most important things to test is the behavior of your website when the Referer header is not sent, as this can have a significant impact on the functionality of your website. Some possible issues to look out for include:

- Broken links or missing resources
- Incorrect tracking or analytics data
- Incomplete forms or incorrect validation messages

## Sample configurations
Here's how to configure the Referrer-Policy header in various web servers:

### IIS
To configure the Referrer-Policy header in IIS, you can use the following code in your web.config file:

```
<system.webServer>
    <httpProtocol>
        <customHeaders>
            <add name="Referrer-Policy" value="strict-origin-when-cross-origin" />
        </customHeaders>
    </httpProtocol>
</system.webServer>
```

### Apache
To configure the Referrer-Policy header in Apache, you can use the following code in your .htaccess file:

```
Header set Referrer-Policy "strict-origin-when-cross-origin"
```

### Nginx
To configure the Referrer-Policy header in Nginx, you can use the following code in your nginx.conf file:

```
add_header Referrer-Policy "strict-origin-when-cross-origin";
```

### Tomcat
To configure the Referrer-Policy header in Tomcat, add the following code to your web application's `web.xml` file:

```
<filter>
  <filter-name>Referrer-Policy</filter-name>
  <filter-class>org.apache.catalina.filters.ReferrerPolicyHeaderFilter</filter-class>
  <init-param>
    <param-name>policy</param-name>
    <param-value>strict-origin-when-cross-origin</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>Referrer-Policy</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

### Traefik
To configure the Referrer-Policy header in Traefik, you can add the following code to your Traefik configuration file:

```
[http.middlewares.referrerPolicy.headers]
  ReferrerPolicy = "no-referrer"
```

Note that you may need to adjust the value for "ReferrerPolicy" to meet your specific needs. You can find more information on the available values and their meanings in the specification for the Referrer-Policy header.

It's important to test your changes to ensure that the header is being sent correctly and is having the desired effect. To test the header, you can use tools like curl or the web developer console in your web browser to examine the headers being sent by your website.

It's also important to monitor your website logs for any error messages or other issues related to the Referrer-Policy header. This will help you catch and resolve any problems quickly, before they have a chance to cause more serious issues.

Overall, the Referrer-Policy header is an important tool for protecting the privacy and security of your website and its users. By carefully configuring and monitoring the header, you can help ensure that your website remains safe and secure. 

