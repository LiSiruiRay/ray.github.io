---
title: 'Startup Log: Upgrading to Secure Tool Call'
date: 2025-10-22 04:27:16
math: true
excerpt: Updating agent's tools from unsecure to secure
tags: [Startup, AI Agent, Tool, Security]
categories:
  - Project Logs
  - Start Up Logs
  - Lessons Learned
---

# Problem:
Before upgrading, our agent calls tools directly from DynamoDB. This led the problem of prompt injection. I upgraded the tools to be mroe secure.

# Old Architecture

```
┌──────────┐                    ┌───────────────┐                    ┌──────────┐ 
│ Frontend │───(userId)────────▶│  Agent Repo   │───(userId)────────▶│ Backend  │
│          │                    │  (Separate)   │                    │ DynamoDB │
└──────────┘                    └───────────────┘                    └──────────┘
                                       │
                                       │ Agent can see userId
                                       │ and manipulate it via
                                       └─ prompt injection ❌
```

# New Architecture

```
┌──────────┐           ┌───────────────┐           ┌────────────────┐           ┌───────────────┐
│ Frontend │           │ AWS Cognito   │           │  Agent Repo    │           │  Your Backend │
│ (logged) │           │ (Auth)        │           │  (Separate)    │           │  API/Tools    │
└────┬─────┘           └──────┬────────┘           └───────┬────────┘           └──────┬────────┘
     │ 0. Exchange auth (code/creds) for JWT               │                           │
     │────────────────────────────────────────────────────▶│                           │
     │ ◀────────────────────────── 0’. Return JWT          │                           │
     │                                                     │                           │
     │ 1. Trigger conversation with JWT in request         │                           │
     ├────────────────────────────────────────────────────▶                            │
     │                                                     │ 2. Tool call includes     │
     │                                                     ├──────────────────────────▶│
     │                                                     │        JWT                │
     │                                                     │                           │
     │                                                     │                           │ 3. Verify JWT
     │                                                     │                           │    (Cognito JWKS)
     │                                                     │                           │
     │                                                     │                           │ 4. If valid, fetch
     │                                                     │                           │    and return data
     │                                                     │                           ├──────────┐
     │                                                     │                           │          │
     │                                                     │                           │  (DB /   │
     │                                                     │                           │ services)│
     │                                                     │                           └──────────┘
     │                                                     │                           │
     │ 5. Agent receives response and returns to UI ◀──────┴───────────────────────────┘
     │
     ▼
 Frontend renders data
```

In this way we prevent the prompt inejction problem.

# Other Options?

We do have other options. This is one of them:
```
┌──────────┐         ┌─────────────────┐         ┌────────────────┐         ┌──────────┐
│ Frontend │────1───▶│ Backend Broker  │────2───▶│  Agent Repo    │     ┌───│ DynamoDB │
│          │         │ (This Repo)     │         │  (Separate)    │     │   └──────────┘
└──────────┘         └─────────────────┘         └────────────────┘     │
                            │                             │             │
                            │ 3. Agent calls              │             │
                            │    financial tools          │             │
                            │    with session_token       │             │
                            │    (NO userId)              │             │
                            │◀────────────────────────────┘             │
                            │                                           │
                            │ 4. Backend validates token                │
                            │    and retrieves userId                   │
                            │                                           │
                            └───────────────────────────────────────────┘
                                      5. Query DynamoDB
```

# Considerations
In this implementation, we will reconstruct the interaction between two side of conversation (chatting) to backend and agent. I first considered this option for the following reasons:

- Front end should not generate any token
- We will have a more flexible and powerful scope management system on teh step 3
- Backend in charge of everything, centralized

Yet benefits comes with cost:

- Streaming from agent -> backend -> frontend will be harder. This requires more engineer work and thus not a good option for a startup like us.
- Token management is a over kill. We have to design another system and provided that a lot of our coding is AI Gen, this potentially reduced reliability, increased complexity
- Backend handle everything is not really neccessary at this stage 