---
title: >-
  Resolving Mixed Content Issues When Serving an API Over HTTPS with Nginx and
  Let's Encrypt
date: 2024-10-23 10:41:55
tags: [Debug, Http, Https, Tech, Tutorial, Web Development]
categories:
- Daily Logs
- Debug Logs
---

# Resolving Mixed Content Issues When Serving an API Over HTTPS with Nginx and Let's Encrypt

## Introduction

While developing a web application, I encountered a **Mixed Content** error when my frontend, served over HTTPS, tried to make API requests to a backend served over HTTP. This is a common issue that arises due to modern browsers blocking insecure content loaded over HTTP from an HTTPS page, as a security measure.

In this blog post, I'll walk through the steps I took to resolve this issue by configuring Nginx to proxy API requests over HTTPS using Let's Encrypt SSL certificates. I'll also cover some challenges I faced along the way and how I overcame them.

---

## The Problem: Mixed Content Error

When I tried to make API calls from my frontend application, which is served over HTTPS, to my backend API served over HTTP, I received the following error in the browser console:

```
Mixed Content: The page at '<https://example.com/>' was loaded over HTTPS, but requested an insecure resource '<http://api.example.com/login>'. This request has been blocked; the content must be served over HTTPS.

```

This error occurs because browsers enforce a security policy called **Mixed Content Blocking**, which prevents secure HTTPS pages from loading resources over an insecure HTTP connection.

---

## Initial Setup

### Existing Nginx Configuration

I already had an Nginx server configured with SSL certificates from Let's Encrypt for my domain `example.com`. The Nginx configuration looked something like this:

```
server {
    server_name example.com;

    location / {
        proxy_pass <http://localhost:3000>;
        # Additional proxy settings...
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    # Additional SSL settings...
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}

```

This configuration was set up to serve the frontend application over HTTPS and redirect all HTTP traffic to HTTPS.

---

## Attempting to Fix the Mixed Content Error

### Understanding the Cause

To resolve the Mixed Content error, I needed to ensure that all resources loaded by my HTTPS page were also served over HTTPS. This meant serving the backend API over HTTPS as well.

### Considering Options

There were a few ways to achieve this:

1. **Enable HTTPS on the Backend API Server**: Configure the backend server to handle HTTPS requests directly.
2. **Use Nginx as a Reverse Proxy**: Configure Nginx to proxy API requests over HTTPS to the backend server, which continues to run over HTTP.

I chose the second option since it's a common practice to use Nginx as a reverse proxy, handling SSL termination and forwarding requests to backend services.

---

## Modifying the Nginx Configuration

### Adding a Proxy for the API

I updated the Nginx configuration to include a `location` block for the API endpoints. Here's the modified configuration:

```
server {
    server_name example.com;

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    # Additional SSL settings...

    # Frontend application
    location / {
        proxy_pass <http://localhost:3000>;
        # Additional proxy settings...
    }

    # Backend API
    location /api/ {
        proxy_pass <http://localhost:8081/api/>;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}

```

### Explanation:

- **location /api/**: This block captures all requests starting with `/api/`.
- *proxy_pass http://localhost:8081/api/**: Proxies these requests to the backend API server running on `localhost` port `8081`.
- **SSL Termination**: Nginx handles the HTTPS connections, decrypting the data and forwarding the requests to the backend over HTTP.

### Testing the Configuration

After saving the changes, I tested the Nginx configuration:

```bash
sudo nginx -t

```

Since there were no errors, I reloaded Nginx:

```bash
sudo systemctl reload nginx

```

---

## Updating the Frontend Application

To ensure that the frontend makes API requests over HTTPS, I updated the API endpoints in the frontend code to use relative paths:

```jsx
const apiUrl = '/api/login';

```

By using relative paths, the frontend will automatically use the same protocol and domain as the page it's running on, ensuring that all requests are made over HTTPS.

---

## Encountering a New Problem

### Testing the API Directly Over HTTPS

Out of curiosity, I tried accessing the backend API directly over HTTPS on port `8081`:

```
<https://api.example.com:8081/api/test>

```

This resulted in an error, and the backend server logs showed a traceback indicating that it couldn't handle the request.

### Backend Error Log:

```
Traceback (most recent call last):
  File "...", line ..., in handle
    req = next(parser)
  File "...", line ..., in __next__
    self.mesg = self.mesg_class(self.cfg, self.unreader, self.source_addr, self.req_count)
  # Additional traceback details...

```

### Understanding the Issue

The backend server wasn't configured to handle HTTPS connections. When I tried to access it over HTTPS, it received encrypted data that it couldn't decrypt, leading to the error.

---

## Resolving the New Problem

### Correct Way to Access the API

I realized that I shouldn't be accessing the backend API directly over HTTPS on port `8081`. Instead, I should access it through Nginx over HTTPS:

```
<https://example.com/api/test>

```

### Explanation

- **Nginx Handles HTTPS**: Nginx is configured with SSL certificates and listens on port `443` for HTTPS connections.
- **Backend Communicates Over HTTP**: The backend server communicates over HTTP with Nginx, and since both are on the same server (or trusted network), this is acceptable.
- **Avoiding Direct Backend Access**: Directly accessing the backend over HTTPS bypasses Nginx and attempts to use a protocol the backend isn't configured for.

### Testing the API via Nginx

I used Postman and my browser to test the API endpoint:

```
<https://example.com/api/test>
```

The API responded as expected, and there were no errors in the backend logs.

---

## Conclusion

By configuring Nginx to proxy API requests over HTTPS and ensuring that the frontend application uses the correct API endpoints, I successfully resolved the Mixed Content error. Additionally, I learned the importance of not attempting to access backend services directly over protocols they're not configured to handle.

---

## Key Takeaways

- **Mixed Content Errors**: Occur when an HTTPS page loads resources over HTTP. To fix this, ensure all resources are loaded over HTTPS.
- **Using Nginx as a Reverse Proxy**: Allows you to handle SSL termination at Nginx and proxy requests to backend services over HTTP.
- **Avoid Direct Backend Access Over HTTPS**: If the backend isn't configured for HTTPS, accessing it directly over HTTPS will cause errors.
- **Update Frontend API Endpoints**: Use relative paths or ensure that all API endpoints use HTTPS to avoid Mixed Content errors.

---

## Additional Notes

- **Security Considerations**: While it's acceptable for Nginx to communicate with backend services over HTTP when they're on the same server or trusted network, always assess your security requirements.
- **SSL Certificate Renewal**: Let's Encrypt certificates expire every 90 days. Ensure you have a cron job or systemd timer set up to renew them automatically.

---

## References

- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/getting-started/)
- [Mixed Content - Web Security | MDN](https://developer.mozilla.org/en-US/docs/Web/Security/Mixed_content)