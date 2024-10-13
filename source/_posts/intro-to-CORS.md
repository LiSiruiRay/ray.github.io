---
title: intro to CORS (Cross-Origin Resource Sharing)
date: 2024-10-13 01:55:55
tags:
---
> I met a prolem with CORS while developing the frontend. Here is how I step by step get deeper and deeper understanding of CORS.

# Understanding CORS: A Developer's Journey from Confusion to Clarity

As web developers, we often encounter perplexing issues that challenge our understanding of how web technologies interact. One such topic that frequently causes confusion is **CORS (Cross-Origin Resource Sharing)**. In this blog post, we'll explore CORS through a real-world conversation between a developer (let's call him Alex) and an experienced mentor. This dialogue will help demystify CORS, explain why it's essential, and provide practical solutions to common problems.

---

## The Initial Challenge: Fetching Data in a React App

**Alex:** *"I'm building a simple React app that fetches data from `https://planner.slray.com/test_summarize` and displays it. However, I'm running into CORS issues. It works fine when I use Python's `requests` library, but I get errors in my React app. What is CORS, and why is this happening?"*

**Mentor:** *"Great question! CORS is a security feature implemented by browsers to control how web pages make requests to a different domain than the one that served the web page. Let's dive into why you're experiencing this issue in your React app but not when using Python's `requests` library."*

---

## What is CORS?

CORS stands for **Cross-Origin Resource Sharing**. It's a security mechanism enforced by web browsers to restrict web pages from making requests to a different domain (origin) than the one that served the web page unless explicitly allowed.

- **Origin Definition:** An origin is defined by the **protocol** (http or https), **domain**, and **port** of a URL.
- **Same-Origin Policy:** By default, browsers implement the same-origin policy, which restricts scripts running on one origin from accessing data from a different origin.

---

## Why Does CORS Affect React Apps but Not Python Scripts?

**Alex:** *"So why does the browser block my React app but not the Python script?"*

**Mentor:** *"That's because the browser enforces the same-origin policy, while Python's `requests` library doesn't. Let's break it down."*

### Browser Environment (React App)

- **Your React App Origin:** `http://localhost:3000`
- **API Origin:** `https://planner.slray.com`
- **Issue:** Since these are different origins, the browser blocks the request unless the server allows it via CORS headers.

### Python Environment

- **Python Scripts:** Run outside the browser, so they're not subject to the same-origin policy.
- **Result:** Python can successfully make requests to any domain without restrictions.

---

## Understanding the CORS Error

When your React app tries to fetch data, you might see an error like:

```
Access to fetch at 'https://planner.slray.com/test_summarize' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

**Explanation:**

- **Browser Blocks the Request:** Due to security policies.
- **No CORS Headers:** The server doesn't include headers to allow cross-origin requests.

---

## Solving the CORS Issue

**Alex:** *"I see. So how can I fix this? Do I need to change the backend code?"*

**Mentor:** *"Exactly. You need to configure the server to include the necessary CORS headers in its responses. This tells the browser it's okay to accept requests from your React app."*

### Modifying the Flask Backend

**Your Flask Server Code:**

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # Enable CORS

@app.route('/test_summarize')
def test_summarize():
    data = {
        'message': 'Hello from the server!',
        # Include your actual data here
    }
    return jsonify(data)  # Ensure the response is in JSON format

if __name__ == '__main__':
    app.run()
```

**Explanation:**

- **CORS(app):** Enables CORS for all routes, allowing your React app to access the API.
- **jsonify:** Ensures the data is properly formatted as JSON.

### Testing the Fix

1. **Restart Your Flask Server:** Apply the changes.
2. **Refresh Your React App:** The CORS error should be resolved.
3. **Check Browser Console:** Ensure no CORS errors are present.

---

## Understanding Why Browsers Enforce CORS

**Alex:** *"Why do browsers enforce CORS? What's the harm if they don't?"*

**Mentor:** *"Great question. Let's explore what could go wrong without CORS."*

### Potential Security Risks Without CORS

#### Cross-Site Request Forgery (CSRF)

- **Scenario:**
  - You're logged into your bank account (`https://mybank.com`).
  - You visit a malicious site (`http://evil.com`).
- **Risk:**
  - `evil.com` could send requests to `mybank.com` using your credentials, performing unauthorized actions like transferring money.
- **CORS Protection:**
  - The browser blocks these cross-origin requests unless `mybank.com` explicitly allows them via CORS headers.

#### Accessing Sensitive Data

- **Scenario:**
  - You're logged into a private service (e.g., email).
  - A malicious site tries to fetch your emails.
- **CORS Protection:**
  - The browser prevents the malicious script from accessing your data.

---

## Alternative Solutions to CORS Issues

**Alex:** *"If modifying the backend isn't an option, are there other ways to solve the CORS problem?"*

**Mentor:** *"Yes, you can use a proxy server or serve your React app from the same origin as your API."*

### Using a Proxy Server

**Set Up a Proxy:**

```javascript
// server.js
const express = require('express');
const request = require('request');
const app = express();

app.get('/api/test_summarize', (req, res) => {
  request(
    { url: 'https://planner.slray.com/test_summarize' },
    (error, response, body) => {
      if (error) {
        res.status(500).send('Error occurred');
      } else {
        res.send(body);
      }
    }
  );
});

app.listen(5000, () => console.log('Proxy server running on port 5000'));
```

**Update React App:**

```javascript
useEffect(() => {
  fetch('/api/test_summarize')
    .then((response) => response.json())
    .then((data) => setData(data))
    .catch((error) => setError(error));
}, []);
```

**Explanation:**

- **Proxy Server:** Acts as an intermediary, bypassing CORS since the browser now communicates with a same-origin server.
- **Browser Security:** Since the proxy is on the same origin, the browser allows the request.

### Serving React and API from the Same Origin

**Modify Flask App:**

```python
from flask import Flask, jsonify, send_from_directory
from flask_cors import CORS
import os

app = Flask(__name__, static_folder='build', static_url_path='/')
CORS(app)

@app.route('/test_summarize')
def test_summarize():
    data = {'message': 'Hello from the server!'}
    return jsonify(data)

@app.route('/')
def serve():
    return send_from_directory(app.static_folder, 'index.html')

if __name__ == '__main__':
    app.run()
```

**Explanation:**

- **Same Origin:** By serving both the React app and API from `https://planner.slray.com`, the browser treats them as the same origin, eliminating CORS issues.

---

## Deepening the Understanding of CORS

**Alex:** *"I think I'm starting to understand. Is CORS like making certain class members private in Java, where you control access through getters and setters to prevent misuse?"*

**Mentor:** *"That's a good analogy. CORS controls access between different domains in a similar way, preventing unauthorized interactions while allowing legitimate ones through proper channels."*

### Why CORS is Essential

- **User Protection:** Prevents malicious sites from accessing sensitive data or performing actions on behalf of users without consent.
- **Controlled Access:** Servers can specify which origins are allowed, similar to how getters and setters control access to private variables.

### What Could Go Wrong Without CORS?

- **Unauthorized Actions:** Malicious scripts could perform actions like transferring funds, accessing personal data, or manipulating content.
- **Data Theft:** Sensitive information could be exposed to unauthorized parties.

---

## Final Thoughts

CORS might seem like an obstacle, but it's a crucial security feature that protects users from malicious activities. Understanding how it works and how to configure it empowers developers to build secure and efficient web applications.

**Key Takeaways:**

- **CORS is Enforced by Browsers:** It's a client-side protection mechanism.
- **Servers Indicate Permissions:** Through CORS headers like `Access-Control-Allow-Origin`.
- **Common Developer Challenge:** Many developers face CORS issues; it's part of building modern web apps.
- **Solutions Exist:** Modifying server configurations, using proxies, or serving apps from the same origin can resolve CORS problems.

---

**Alex:** *"Thanks for the explanation! I now understand why CORS is important and how to handle it in my projects."*

**Mentor:** *"You're welcome! Remember, understanding the 'why' behind these mechanisms helps you become a better developer."*

---

By following this journey from confusion to clarity, we hope you've gained a better understanding of CORS and its significance in web development. Whether you're building a simple app or a complex web application, knowing how to navigate CORS issues is an invaluable skill.