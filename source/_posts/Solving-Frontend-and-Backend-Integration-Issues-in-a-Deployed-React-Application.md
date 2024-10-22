---
title:
  Solving Frontend and Backend Integration Issues in a Deployed React
  Application
date: 2024-10-21 23:52:01
tags:
---
When building modern web applications with frameworks like React and Next.js, integrating the frontend with the backend can sometimes lead to unexpected issues, especially during deployment. In this blog post, we'll explore a common problem developers face when deploying their applications, understand why it happens, and discuss how to resolve it effectively.

## **The Problem**

After deploying a React frontend application to a server (e.g., an AWS EC2 instance), you might encounter the following error in the browser console:

```
Failed to load resource: net::ERR_CONNECTION_REFUSED

```

This error suggests that the application is trying to access a resource on `localhost`, which isn't available in the production environment. You might wonder:

- **Why is my frontend, deployed on a server, still trying to access `localhost`?**
- **Is the frontend code running on the server or in the browser?**
- **How can I secure my API endpoints if they are exposed in the frontend code?**
- **Can I use relative paths in API calls when my frontend and backend are running on different ports?**

Let's dive into these questions and explore how to solve the underlying issues.

## **Understanding the Frontend Execution Environment**

First, it's crucial to understand where your frontend code runs. Even though you've deployed your React application to a server, the JavaScript code runs **in the user's browser**, not on your server. When a user accesses your website, the server serves the static files (HTML, CSS, JavaScript) to the user's browser, which then executes the JavaScript code.

### **Why Is This Important?**

- **API Calls from the Browser:** Any API calls made in your frontend code are executed from the user's browser.
- **`localhost` in the Browser Context:** When your frontend code tries to access `http://localhost:8081`, it's referring to `localhost` on the user's machine, not your server.
- **Resulting Error:** Since the user's machine isn't running your backend server on port `8081`, the browser cannot establish a connection, leading to the `ERR_CONNECTION_REFUSED` error.

## **The Misconfigured API Endpoint**

Consider the following simplified code snippet from your frontend application:

```jsx
const response = await fetch('<http://localhost:8081/api/users_data/login>', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ email, password }),
});

```

This code works fine during local development because both the frontend and backend servers are running on your local machine. However, in production, `localhost` doesn't refer to your server but to the client's machine.

### **Solution: Update the API Endpoint**

You need to update the API endpoint in your frontend code to point to the backend server's public address or domain name. For example:

```jsx
const response = await fetch('<https://your-backend-domain.com/api/users_data/login>', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ email, password }),
});

```

Alternatively, you can use environment variables to manage different configurations for development and production environments.

```jsx
// In your .env file
REACT_APP_API_BASE_URL=https://your-backend-domain.com

// In your code
const API_BASE_URL = process.env.REACT_APP_API_BASE_URL;

const response = await fetch(`${API_BASE_URL}/api/users_data/login`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ email, password }),
});

```

## **Security Concerns with Exposed API Endpoints**

You might be concerned that exposing your API endpoints in the frontend code makes your application vulnerable to attacks. Here's what you need to know:

### **API Endpoint Exposure Is Normal**

- **Necessary for Communication:** The frontend needs to know where to send API requests. Therefore, the API endpoints must be accessible in the frontend code.
- **Not Inherently Dangerous:** Knowing an API endpoint doesn't provide an attacker with the means to exploit it if proper security measures are in place.

### **Securing Your API**

To ensure your API remains secure:

- **Implement Authentication:** Use tokens like JSON Web Tokens (JWT) to authenticate requests.
- **Use HTTPS:** Serve your API over HTTPS to encrypt data in transit.
- **Validate Inputs:** Always validate and sanitize inputs on the server side.
- **Set Up CORS Properly:** Configure Cross-Origin Resource Sharing (CORS) to control which origins can access your API.

## **Implementing JWT Authentication**

Using JWT is a common practice to secure APIs. Here's how you can implement it:

### **Backend Changes**

- **Issue JWTs on Authentication:** When a user logs in, generate a JWT and send it to the client.
- **Protect Endpoints:** Require a valid JWT for accessing protected API endpoints.
- **Token Verification:** Verify the JWT on each request to ensure it's valid and not expired.

### **Frontend Changes**

- **Store the JWT Securely:** Store the token in a secure manner, such as in `localStorage` or an HTTP-only cookie.
- **Include JWT in Requests:** Attach the JWT to the `Authorization` header in API requests.

```jsx
// After login
localStorage.setItem('token', data.token);

// When making API calls
const token = localStorage.getItem('token');

const response = await fetch(`${API_BASE_URL}/api/protected-route`, {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json',
  },
});

```

### **Example: Secure Login Function**

```jsx
const handleSubmit = async (e) => {
  e.preventDefault();

  const response = await fetch(`${API_BASE_URL}/api/users_data/login`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ email, password }),
  });

  const data = await response.json();

  if (response.ok) {
    localStorage.setItem('token', data.token);
    // Redirect or update UI accordingly
  } else {
    // Handle errors
  }
};

```

## **Using Relative Paths with Different Ports**

You might also wonder if you can use relative paths in your API calls when your frontend and backend are running on different ports (e.g., frontend on port `3000`, backend on port `8081`).

### **The Challenge**

- **Same-Origin Policy:** Browsers enforce a security policy that restricts scripts from interacting with resources from a different origin (scheme, domain, or port).
- **CORS Issues:** If you try to use relative paths, the browser will attempt to access the backend on the same origin and port as the frontend, which won't work if the backend is on a different port.

### **Solution: Configure a Proxy**

During development, you can configure a proxy to route API requests to the backend server.

### **For Create React App**

In your `package.json`, add:

```json
"proxy": "<http://localhost:8081>"

```

Then, you can use relative paths in your API calls:

```jsx
const response = await fetch('/api/users_data/login', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ email, password }),
});

```

### **For Next.js**

In your `next.config.js`:

```jsx
module.exports = {
  async rewrites() {
    return [
      {
        source: '/api/:path*',
        destination: '<http://localhost:8081/api/:path*>',
      },
    ];
  },
};

```

### **In Production**

In production, you can use a reverse proxy like Nginx to serve both your frontend and backend from the same domain, allowing you to use relative paths without CORS issues.

## **Configuring CORS**

If your frontend and backend are on different domains or ports in production, you need to configure CORS on your backend.

### **Example with Express.js**

```jsx
const express = require('express');
const cors = require('cors');
const app = express();

app.use(cors({
  origin: '<https://your-frontend-domain.com>',
  credentials: true,
}));

```

### **Security Considerations**

- **Specify Allowed Origins:** Only allow your frontend domain to access your API.
- **Avoid Using Wildcards in Production:** Do not set `origin: '*'` in production environments.

## **Conclusion**

Deploying a React frontend application and integrating it with a backend server requires careful consideration of how the frontend code executes and how API calls are handled. By understanding that the frontend code runs in the user's browser and adjusting your API endpoints accordingly, you can avoid common pitfalls.

### **Key Takeaways**

- **Frontend Code Runs in the Browser:** Even when deployed on a server, the JavaScript code is executed in the user's browser.
- **Avoid Using `localhost` in API Calls:** Update API endpoints to point to your backend server's public domain or IP address.
- **Secure Your API Endpoints:** Use authentication methods like JWT to protect your API.
- **Configure Proxies for Development:** Use proxies to handle cross-origin requests during development.
- **Properly Configure CORS:** Ensure your backend is set up to handle requests from your frontend domain.

By following these practices, you can build a secure and reliable web application that works seamlessly in both development and production environments.

## **Additional Resources**

- [Understanding CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
- [Using JWT for Authentication](https://jwt.io/introduction/)
- [Create React App Proxying API Requests](https://create-react-app.dev/docs/proxying-api-requests-in-development/)
- [Next.js API Routes](https://nextjs.org/docs/api-routes/introduction)
- [Securing Node.js Applications](https://www.npmjs.com/package/helmet)

---

**Note:** Always ensure you're following best security practices when handling user data and authentication. Regularly update your dependencies and stay informed about security vulnerabilities in the tools and libraries you use.