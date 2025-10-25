---
title: 'Product Update: secure financial tool calling'
date: 2025-10-24 21:22:20
math: true
excerpt: Secured tool calling with token propagation -- debugging tour
tags: [Startup, EQUO, AI Agent, Tool, Security, Security, JWT]
categories:
  - Project Logs
  - Start Up Logs
  - Product Update
---

# The Token Propagation Debugging Journey: Technical Deep-Dive

## **Context: Post-Implementation**

**Status**: We had successfully implemented 5 secure tools and all unit tests were passing 

```python
# Unit test (PASSING)
def test_get_user_spending_analysis_secure_tool_with_real_token(self):
    mock_config = {
        "configurable": {"thread_id": "test-thread", "auth_token": self.test_token}
    }
    result = get_user_spending_analysis_secure_tool(days=30, config=mock_config)
    assert "Spending Analysis" in result  # ✅ PASSED
```

**But when the agent tried to use the tools...**

```bash
# Agent Log
ERROR - No authentication token found in context
```

**The Mystery**: Why did direct calls work but agent calls fail?

---

## **Attempt 1: ContextVar (Thread-local Context)**

### **The Theory**

Python's `contextvars` provides thread-safe, async-aware storage. Perfect for passing data through call stacks!

```python
# token_context.py
from contextvars import ContextVar

_auth_token: ContextVar[str] = ContextVar("auth_token", default=None)

def set_auth_token(token: str, conversation_id: Optional[str] = None) -> None:
    _auth_token.set(token)  # Store in current context
    logger.info("Set authentication token in context")

def get_auth_token(conversation_id: Optional[str] = None) -> str:
    token = _auth_token.get()  # Retrieve from current context
    if not token:
        raise UserContextError("No authentication token set")
    return token
```

### **The Implementation**

```python
# financial_routes.py (Entry point)
@routes.post("/api/v1/financial/chat-messages")
async def financial_chat_streaming(chat_request: ChatRequest):
    token = request.headers.get("Authorization").split(" ")[1]
    
    # Set token in context
    if chat_request.conversation_id:
        set_auth_token(token, chat_request.conversation_id)
        logger.info(" Set auth token in context")
    
    async for event in financial_manager.process_streaming_message(...):
        yield event
```

### **The Tool**

```python
# secure_personal_tools.py
@requires_auth_token  # Decorator that calls get_auth_token()
def get_user_spending_analysis_secure_tool(days: int = 30) -> str:
    # This should automatically get token from context
    response = _make_authenticated_request(...)
```

### **The Error**

```bash
# Logs when agent called the tool
2025-10-24 06:27:07,526 - token_context - INFO - ✅ Set auth token in context
2025-10-24 06:27:18,788 - secure_personal_tools - INFO - get_user_spending_analysis_secure_tool called
2025-10-24 06:27:18,788 - token_context - ERROR - ❌ No authentication token found in context
```

**Timeline**: 11 seconds passed between setting and getting!

### **Why It Failed**

**ContextVar scope**: `ContextVar` is tied to the **execution context** (async task/thread). When LangGraph creates worker nodes:

```python
# What LangGraph does internally (simplified)
async def invoke_tool(tool, args):
    # This might run in a NEW async context
    result = await tool(**args)
    return result
```

Each new async context has its own `ContextVar` storage. The token we set in the Flask request context doesn't automatically propagate to the LangGraph worker context.

**Technical Issue**: **Context boundary crossing** - `ContextVar` doesn't propagate across `asyncio.create_task()` or executor boundaries without explicit copying.

---

## **Attempt 2: Global Dictionary with Conversation ID**

### **The Theory**

"If context doesn't work, let's use a global dictionary! Everyone can access global state."

```python
# token_context.py
_token_by_conversation: Dict[str, str] = {}  # Global dictionary
_token_lock = threading.Lock()  # Thread-safe access

def set_auth_token(token: str, conversation_id: str) -> None:
    with _token_lock:
        _token_by_conversation[conversation_id] = token
        logger.info(f"✅ Stored token for conversation {conversation_id}")
        logger.info(f"📋 Dictionary now has keys: {list(_token_by_conversation.keys())}")

def get_auth_token(conversation_id: str) -> str:
    with _token_lock:
        logger.info(f"📋 Dictionary has keys: {list(_token_by_conversation.keys())}")
        token = _token_by_conversation.get(conversation_id)
    
    if not token:
        raise UserContextError(f"No token for conversation {conversation_id}")
    return token
```

### **The Implementation**

```python
# secure_personal_tools.py
def get_user_spending_analysis_secure_tool(days: int = 30) -> str:
    # Get conversation_id somehow and look up token
    conversation_id = _conversation_id.get()  # From ContextVar
    auth_token = get_auth_token(conversation_id)
    ...
```

### **The Error - Round 1**

```bash
# Logs
2025-10-24 06:27:07,526 - token_context - INFO - ✅ Stored token for conversation abc-123
2025-10-24 06:27:07,526 - token_context - INFO - 📋 Dictionary now has keys: ['abc-123']

# 11 seconds later in the tool
2025-10-24 06:27:18,788 - token_context - INFO - 📋 Dictionary has keys: []
2025-10-24 06:27:18,788 - token_context - ERROR - ❌ No token for conversation abc-123
```

**Wait, what?!** The dictionary was populated but now it's empty?

### **Adding Diagnostic Logging**

```python
def set_auth_token(token: str, conversation_id: str) -> None:
    import os
    current_pid = os.getpid()
    current_thread = threading.current_thread().ident
    
    logger.info(f"🔐 set_auth_token called from PID:{current_pid} thread:{current_thread}")
    logger.info(f"🔍 Module dict ID: {id(_token_by_conversation)}")  # Memory address!
    
    with _token_lock:
        _token_by_conversation[conversation_id] = token
        logger.info(f"✅ Stored token")

def get_auth_token(conversation_id: str) -> str:
    import os
    current_pid = os.getpid()
    current_thread = threading.current_thread().ident
    
    logger.info(f"🔍 get_auth_token called from PID:{current_pid} thread:{current_thread}")
    logger.info(f"🔍 Module dict ID: {id(_token_by_conversation)}")
    
    with _token_lock:
        logger.info(f"📋 Dictionary has keys: {list(_token_by_conversation.keys())}")
        token = _token_by_conversation.get(conversation_id)
    ...
```

### **The Smoking Gun**

```bash
# Set phase
2025-10-24 06:27:07,526 - INFO - 🔐 set_auth_token called from PID:66990 thread:6127906816
2025-10-24 06:27:07,526 - INFO - 🔍 Module dict ID: 4672419072  ⬅️ Memory address
2025-10-24 06:27:07,526 - INFO - ✅ Stored token
2025-10-24 06:27:07,526 - INFO - 📋 Dictionary now has keys: ['abc-123']

# Get phase (in tool execution)
2025-10-24 06:27:18,788 - INFO - 🔍 get_auth_token called from PID:66990 thread:8234567890
2025-10-24 06:27:18,788 - INFO - 🔍 Module dict ID: 4689234176  ⬅️ DIFFERENT ADDRESS!!
2025-10-24 06:27:18,788 - INFO - 📋 Dictionary has keys: []
```

### **The Discovery**

**The dictionaries have different memory addresses!** This means the Python module `token_context.py` was **imported twice**, creating two separate instances of `_token_by_conversation`!

**Why This Happens**: 

```python
# Scenario 1: Flask main process
from equo_agent.utils.token_context import set_auth_token
# Creates: _token_by_conversation @ memory address 4672419072

# Scenario 2: LangGraph worker (could be subprocess, thread pool, or fork)
from equo_agent.utils.token_context import get_auth_token
# Creates: NEW _token_by_conversation @ memory address 4689234176
```

**Root Cause**: 
- **Flask's reloader** creates multiple processes
- **LangGraph's execution model** may use thread pools or separate execution contexts
- Each execution context gets its own import of the module
- Python's module system creates separate instances in different processes/contexts

**Technical Term**: **Module Singleton Anti-pattern** - Global dictionaries in modules are only "global" within that process/import context.

---

## **Attempt 3: Thread ID Mapping**

### **The Theory**

"If `conversation_id` lookup doesn't work across contexts, let's map thread IDs to conversation IDs!"

```python
# token_context.py
_token_by_conversation: Dict[str, str] = {}
_thread_to_conversation: Dict[int, str] = {}  # New: thread ID → conversation ID
_token_lock = threading.Lock()
_thread_lock = threading.Lock()

def set_auth_token(token: str, conversation_id: str) -> None:
    # Store by conversation ID
    with _token_lock:
        _token_by_conversation[conversation_id] = token
    
    # Map current thread to conversation
    thread_id = threading.current_thread().ident
    if thread_id:
        with _thread_lock:
            _thread_to_conversation[thread_id] = conversation_id
            logger.info(f"🔗 Mapped thread {thread_id} to conversation {conversation_id}")

def get_auth_token(conversation_id: Optional[str] = None) -> str:
    current_thread_id = threading.current_thread().ident
    
    # If no conversation_id provided, look it up from thread mapping
    if not conversation_id:
        with _thread_lock:
            conversation_id = _thread_to_conversation.get(current_thread_id)
            logger.info(f"🔍 Retrieved conversation_id {conversation_id} from thread {current_thread_id}")
    
    if conversation_id:
        with _token_lock:
            token = _token_by_conversation.get(conversation_id)
            if token:
                return token
    
    raise UserContextError("No authentication token found")
```

### **The Error**

```bash
# Set phase
2025-10-24 06:27:07,526 - INFO - 🔗 Mapped thread 6127906816 to conversation abc-123
2025-10-24 06:27:07,526 - INFO - 📋 _thread_to_conversation: {6127906816: 'abc-123'}

# Get phase (different thread)
2025-10-24 06:27:18,788 - INFO - 🔍 get_auth_token called from thread:8234567890
2025-10-24 06:27:18,788 - INFO - 📋 Available thread mappings: {6127906816: 'abc-123'}
2025-10-24 06:27:18,788 - INFO - ❌ No conversation_id found for thread 8234567890
```

### **Why It Failed**

**Thread pool behavior**: LangGraph uses different threads for:
- Flask request handling (thread A)
- LangGraph graph execution (thread B)  
- Tool execution within react agents (thread C)

Even if we mapped thread A → conversation, thread C doesn't know about this mapping (and again, might be looking at a different copy of the dictionary!).

**Technical Issue**: **Thread affinity assumption** - We assumed thread-local storage would persist, but async systems routinely switch threads.

---

## **Attempt 4: State-Based Propagation (First Try)**

### **The Theory**

"LangGraph has a state system! Let's add the token to the state!"

```python
# financial_manager.py
async def _run_real_financial_analysis_streaming(
    self, query, user_id, conversation_id, ..., auth_token
):
    initial_state = {
        "messages": messages,
        "user_id": user_id,
        "auth_token": auth_token,  # ✅ Add token to state!
        "current_step": "world",
        # ...
    }
    
    async for stream_data in graph.astream(initial_state, config):
        yield stream_data
```

### **Using InjectedState**

```python
# secure_personal_tools.py
from langgraph.prebuilt import InjectedState
from typing import Annotated

def get_user_spending_analysis_secure_tool(
    days: int = 30,
    state: Annotated[dict, InjectedState] = None,  # Inject state
) -> str:
    # Get token from state
    auth_token = None
    if state:
        auth_token = state.get("auth_token")
        logger.info(f"🔑 Retrieved auth_token from state: {bool(auth_token)}")
    
    if not auth_token:
        return "Authentication failed: No authentication token available"
    
    # Use token...
```

### **The Error - First Discovery**

```bash
# Agent log
2025-10-24 06:27:18,788 - secure_personal_tools - INFO - get_user_spending_analysis_secure_tool called
2025-10-24 06:27:18,788 - secure_personal_tools - INFO - 🔍 State keys: ['messages', 'is_last_step', 'remaining_steps']
2025-10-24 06:27:18,788 - secure_personal_tools - INFO - 🔑 Retrieved auth_token from state: False
2025-10-24 06:27:18,788 - secure_personal_tools - ERROR - ❌ No auth_token found in state
```

**The state exists but `auth_token` is NOT in it!**

### **Why It Failed**

**LangGraph's `create_react_agent` uses a fixed state schema**:

```python
# What create_react_agent does internally (simplified)
class MessagesState(TypedDict):
    messages: Annotated[Sequence[BaseMessage], add_messages]
    is_last_step: bool
    remaining_steps: int
    # That's it! No custom fields allowed!

def create_react_agent(llm, tools):
    # Creates a graph with MessagesState
    graph = StateGraph(MessagesState)
    # ... agent logic
    return graph.compile()
```

Our `auth_token` in the main supervisor graph's state **doesn't propagate** to the react agent sub-graphs because:

1. **Supervisor graph** has custom state: `{"messages": ..., "auth_token": "xyz", ...}`
2. **React agent sub-graph** only accepts `MessagesState` fields
3. The `auth_token` is **dropped** when entering the react agent!

**Technical Issue**: **State schema mismatch** - Nested graphs with different state schemas don't automatically merge custom fields.

---

## **Attempt 5: Bad Import Error**

### **The Confusion**

After reading LangGraph docs, we tried:

```python
# secure_personal_tools.py (WRONG IMPORT)
from langgraph.prebuilt.chat_agent_executor import InjectedState  # ❌ Wrong!
```

### **The Error**

```bash
# Agent completely stuck - no logs!
2025-10-24 06:27:07 - INFO - Created supervisor graph
# ... silence ...
# (Agent never starts processing)
```

**Why**: The wrong import caused an initialization error that prevented the entire agent from starting.

### **The Fix**

```python
from langgraph.prebuilt import InjectedState  # ✅ Correct import
```

But then we tried:

```python
def get_user_spending_analysis_secure_tool(
    days: int = 30,
    config: RunnableConfig = None,  # ❌ Still not injected properly
) -> str:
    ...
```

Without `InjectedState`, the `config` parameter wasn't being injected by LangGraph - it was just `None`!

---

## **The Final Solution: RunnableConfig (The Right Way)**

### **The Breakthrough**

Reading LangGraph documentation more carefully:

> **RunnableConfig** is the standard way to pass configuration to all runnables, tools, and nodes in a graph. It's guaranteed to propagate through all execution layers.

### **The Implementation**

#### **Step 1: Pass Token in Config**

```python
# financial_manager.py
async def _run_real_financial_analysis_streaming(..., auth_token):
    # Don't put token in state - put it in CONFIG!
    config = {
        "configurable": {
            "thread_id": conversation_id,
            "auth_token": auth_token,  # ✅ In config, not state
        }
    }
    
    async for stream_data in graph.astream(initial_state, config):
        yield stream_data
```

#### **Step 2: Tools Receive Config Automatically**

```python
# secure_personal_tools.py
from langchain_core.runnables import RunnableConfig

def get_user_spending_analysis_secure_tool(
    days: int = 30,
    config: RunnableConfig = None,  # LangGraph injects this automatically!
) -> str:
    logger.info(f"🔍 Config keys: {list(config.get('configurable', {}).keys())}")
    
    # Get token from config
    auth_token = None
    if config and "configurable" in config:
        auth_token = config["configurable"].get("auth_token")
        logger.info(f"🔑 Retrieved auth_token from config: {bool(auth_token)}")
    
    if not auth_token:
        return "Authentication failed: No authentication token available"
    
    # Make authenticated request
    response_data = _make_authenticated_request(
        endpoint="/api/financial-agent/get-spending-analysis",
        auth_token=auth_token,
    )
    return format_response(response_data)
```

### **The Success**

```bash
# Agent log - IT WORKS!
2025-10-24 06:34:35,315 - secure_personal_tools - INFO - get_user_spending_analysis_secure_tool called
2025-10-24 06:34:35,315 - secure_personal_tools - INFO - 🔍 Config keys: ['thread_id', 'auth_token', '__pregel_task_id', '__pregel_send', ...]
2025-10-24 06:34:35,315 - secure_personal_tools - INFO - 🔑 Retrieved auth_token from config: True
2025-10-24 06:34:35,315 - secure_personal_tools - INFO - Making authenticated POST request to /api/financial-agent/get-spending-analysis
2025-10-24 06:34:37,551 - secure_personal_tools - INFO - ✅ Successfully received response
```

### **Why This Works**

**RunnableConfig propagation path**:

```
financial_routes.py (config created)
    ↓ config passed to astream()
financial_manager.py (graph.astream)
    ↓ config flows through graph
supervisor (receives config)
    ↓ config passed to worker
personal_agent (react agent, receives config)
    ↓ config passed to tool invocation
get_user_spending_analysis_secure_tool (receives config)
    ↓ tool extracts auth_token
    ✅ SUCCESS
```

**Key Properties of RunnableConfig**:
1. **Guaranteed propagation**: LangGraph's core design ensures config reaches all layers
2. **Works across boundaries**: Crosses async contexts, thread pools, and subgraph boundaries
3. **Schema-agnostic**: Doesn't depend on state schema - it's a separate channel
4. **Standard pattern**: This is how LangGraph is designed to pass cross-cutting concerns

**Technical Explanation**: 

LangGraph's execution model uses a **config parameter** that's explicitly threaded through every execution layer:

```python
# LangGraph internal flow (simplified)
def invoke_node(node, state, config):  # config is always passed
    return node.invoke(state, config=config)

def invoke_tool(tool, args, config):  # config is always passed
    return tool(**args, config=config)
```

Unlike state (which can be schema-restricted) or module globals (which suffer from import isolation), the `config` parameter is **explicitly passed** through the call chain, making it reliable across all execution contexts.

---

## **Summary: Technical Comparison**

| Approach | Technical Issue | Why It Failed |
|----------|----------------|---------------|
| **ContextVar** | Context boundary crossing | Doesn't propagate across `asyncio.create_task()` boundaries |
| **Global Dict** | Module import isolation | Multiple imports created separate dictionary instances |
| **Thread Mapping** | Thread affinity assumption | Thread pools switch threads; mapping becomes stale |
| **State (InjectedState)** | State schema mismatch | React agents use fixed `MessagesState` schema |
| **RunnableConfig** ✅ | None - correct approach | Explicitly propagated through all layers by design |

---

## **Key Technical Learnings**

1. **Async contexts are isolated**: `ContextVar` and thread-local storage don't automatically propagate across async boundaries
2. **Module globals aren't truly global**: In multi-process/multi-import scenarios, each context gets its own module instance
3. **State schemas are enforced**: LangGraph's nested graphs enforce their own state schemas
4. **Use the framework's patterns**: LangGraph designed `RunnableConfig` specifically for cross-cutting concerns like authentication
5. **Diagnostic logging is crucial**: Memory addresses (`id()`) and thread IDs helped identify the root cause

---

## **The Debug Workflow**

Each round followed this pattern:

1. **Hypothesis**: "Maybe X will work..."
2. **Implementation**: Write the code
3. **Test**: Restart server, invoke agent
4. **Log Analysis**: Read diagnostic logs
5. **Discovery**: "Oh! The memory addresses are different!"
6. **New Hypothesis**: "That means..."
7. **Repeat**: 20 times

The key breakthrough was **adding diagnostic logging** (memory addresses, thread IDs) that revealed the underlying execution model's behavior.