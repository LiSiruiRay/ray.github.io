---
title: WebSocket on stock data with latency issue
date: 2023-10-17 14:13:08
excerpt: WebSocket Latency issues with REAL WORLD SITUATION & SOLUTION
tags: [Learning Log, WebSocket]
categories:
  - Daily Logs
  - Learning Logs
---

# WebSocket on stock data with latency issue

This is a record of me learning WebSocket way of extracting low-latency stock price data.

# Python&WebSocket

I will use the example of extracting stock data to demonstrate the process.

---

WebSockets allow for two-way communication with a remote host, and they're perfect for receiving real-time stock data updates.

Here's a step-by-step guide using the Python `websocket` library:

1. **Installation**:
   First, you'll need to install the `websocket-client` library. You can do this via `pip`:

```bash
pip install websocket-client
```

1. **Connecting to the WebSocket**:
   You'll establish a connection to the WebSocket using the provided URL.
2. **Sending & Receiving Messages**:
   Once the connection is established, you can send and receive messages.

Here's a basic example:

```python
import websocket
import json

# Callbacks for the WebSocket
def on_message(ws, message):
    print(f"Received: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws, close_status_code, close_msg):
    print("WebSocket Closed")

def on_open(ws):
    # This function runs when the connection is established.
    # You can send a message or set up whatever you need.
    print("WebSocket Opened")
    # For example, to send a message:
    # ws.send("Your message here")

# Set up the WebSocket connection
ws = websocket.WebSocketApp(
    "ws://example.com/socket",
    on_message=on_message,
    on_error=on_error,
    on_close=on_close,
)
ws.on_open = on_open

# Keep the connection running
ws.run_forever()

```

- `on_message`: This function is called whenever a new message is received from the WebSocket.
- `on_error`: This function is called when there's an error with the WebSocket.
- `on_close`: This function is called when the WebSocket is closed.
- `on_open`: This function is called when the WebSocket connection is successfully established.

With this script, every time the WebSocket receives a message (e.g., a stock price update), it will print that message to the console.

1. **Keeping the program running**:
   Yes, once the connection is established, you have to keep your program running to continue receiving messages. The `run_forever()` function keeps the program running and the WebSocket connection alive. If you stop the program, you'll disconnect from the WebSocket.

**Note**: Always be careful when hardcoding API keys or sharing them. They should be kept secret. It's good practice to load them from environment variables or external configuration files.

## Send a message

To send a message using the `websocket` library in Python, you'll use the `send` method of the WebSocket object. The message typically needs to be a string, so if you're sending a JSON message like the one you provided, you'll need to serialize it to a string using `json.dumps()`.

Here's how you can integrate message sending into the previous code:

```python
import websocket
import json

# Callbacks for the WebSocket
def on_message(ws, message):
    print(f"Received: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws, close_status_code, close_msg):
    print("WebSocket Closed")

def on_open(ws):
    print("WebSocket Opened")

    # Send subscription message
    message = {
        "action": "subscribe",
        "params": {
            "symbols": "AAPL,RY,RY:TSX,EUR/USD,BTC/USD"
        }
    }

    ws.send(json.dumps(message))

# Set up the WebSocket connection
ws = websocket.WebSocketApp(
    "ws://example.com/socket",
    on_message=on_message,
    on_error=on_error,
    on_close=on_close,
)
ws.on_open = on_open

# Keep the connection running
ws.run_forever()

```

When the WebSocket connection is opened, the `on_open` callback is triggered. Within that function, I've added the code to send your sample message. The message is first defined as a Python dictionary and then converted to a JSON string using `json.dumps()`. After that, it's sent to the WebSocket using the `send` method.

## But how can I see what message the server sent me?

In the example code I provided, every time the server sends a message to your WebSocket client, the `on_message` callback function is triggered. Inside this function, we've set up a simple print statement to display the message:

```python
def on_message(ws, message):
    print(f"Received: {message}")
```

With this setup, any message received from the server will be printed to the console.

However, if you'd like to process or analyze the messages in some way, you can modify the `on_message` function. For example, if the server sends JSON messages and you want to convert them to a Python dictionary to extract certain data, you can do the following:

```python
def on_message(ws, message):
    data = json.loads(message)
    # Now 'data' is a Python dictionary. You can access its elements like:
    # value = data['someKey']

    print(f"Received: {data}")
```

In this modified version, the received message is deserialized from a JSON string into a Python dictionary using `json.loads()`. Then you can access and process the data within the dictionary as needed.

# Inter-process communication

If you want to maintain a connection in one script and send messages from another script, you'll need a mechanism for inter-process communication.

Here's one approach using a combination of a WebSocket in one script and a message queue in another script:

1. **WebSocket script**: This script establishes and maintains the WebSocket connection. It also listens for new messages to send from a message queue (e.g., using Python's `queue.Queue`).
2. **Command-line script**: This script allows you to enter new messages and adds them to the queue.

Both scripts communicate using the shared message queue.

### Example Implementation:

**1. websocket_script.py**:

```python
import websocket
import json
import queue
import threading

# Shared message queue
msg_queue = queue.Queue()

def on_message(ws, message):
    print(f"Received: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws):
    print("WebSocket Closed")

def on_open(ws):
    print("WebSocket Opened")

    # Thread to listen for new messages from the queue
    def send_from_queue():
        while True:
            message = msg_queue.get()
            ws.send(json.dumps(message))
            msg_queue.task_done()

    threading.Thread(target=send_from_queue).start()

ws = websocket.WebSocketApp(
    "ws://example.com/socket",
    on_message=on_message,
    on_error=on_error,
    on_close=on_close,
)
ws.on_open = on_open
ws.run_forever()

```

**2. command_line_script.py**:

```python
import queue

# Use the same shared message queue as the WebSocket script
msg_queue = queue.Queue()

while True:
    user_input = input("Enter message action (e.g., 'subscribe'): ")
    if user_input:
        # Construct your message here based on the input
        message = {
            "action": user_input,
            "params": {
                "symbols": "AAPL,RY,RY:TSX,EUR/USD,BTC/USD"
            }
        }
        msg_queue.put(message)

```

### Instructions:

1. Start `websocket_script.py` first to establish the WebSocket connection.
2. Then, run `command_line_script.py`. This script will prompt you to enter new messages. Whenever you enter a message, it gets added to the queue, and the WebSocket script picks it up and sends it to the server.

Note: This example uses a simple shared `queue.Queue()` to demonstrate the concept. In practice, especially if the scripts were to run in different environments, you'd use something like a message broker (e.g., RabbitMQ, Redis) or some other IPC (Inter-Process Communication) mechanism.

# Latency from processing data

### Situation1

websocket send me 100 messages in 3 second. But I wrote a function to process each message, in the `on_message()` function provided, this function takes 2 second to process message, then how is the rest of the message doing? For example, I have 100 messages in 3 second, but I need to kind of like wait for 2 second for the first message to be processed. Will I loss the result of 99 messages or something?

### Solution1

If the processing of each message takes a significant amount of time (like the 2 seconds per message in your example), the WebSocket's event-driven model can indeed be a bottleneck. However, the WebSocket library won't drop messages because they arrive too quickly; instead, messages will be queued up and processed one at a time as the `on_message` handler is called. Here's a breakdown of the behavior:

1. **WebSocket Message Queue**: The underlying WebSocket library will maintain an internal message queue. As messages come in from the server, they're placed in this queue. The `on_message` handler is called for each message in the order they arrived.
2. **Processing Time**: If processing each message takes 2 seconds, then the `on_message` handler will be occupied for 2 seconds for each message. During this time, new incoming messages will be queued up but not processed. After the current message is finished processing, the next message in the queue will be passed to the `on_message` handler.
3. **Potential Issues**:
   - **Latency**: There will be a growing delay (latency) between when a message is received and when it is processed. In your example, the 100th message will be delayed by almost 200 seconds (2 seconds \* 100) before it's processed.
   - **Memory Consumption**: If messages arrive faster than they can be processed for an extended period, this can lead to increased memory consumption as the queue grows.
4. **Solutions**:
   - **Multithreading/Asynchronous Processing**: Use threads or asynchronous tasks to process messages in parallel. This way, the `on_message` handler can quickly hand off the processing to another thread/task and be ready for the next message almost immediately.
   - **Filtering Messages**: If not all messages need to be processed, you can add a filtering mechanism to discard unneeded messages before processing.
   - **Optimizing Processing**: Look for ways to make the processing function faster.
   - **Distributed Processing**: For more advanced scenarios, consider using distributed processing systems (like Celery) to offload message processing to worker nodes.

Here's a quick example using threading to process messages:

```python
import websocket
import json
import threading

def process_message(message):
    # Your 2-second processing code here
    pass

def on_message(ws, message):
    threading.Thread(target=process_message, args=(message,)).start()

# ... rest of the WebSocket code ...
```

In this example, each message starts a new thread for processing, allowing `on_message` to return almost immediately. This prevents the growing latency issue, but be cautious about starting too many threads at once; there's overhead associated with each thread. If you expect to handle a large volume of messages, consider using a thread pool or a more advanced solution like asynchronous processing or distributed task queues.

### Situation2

If I am trying to store all the data to my database (postgres, locally), what if that time exceeded the data coming time? Does postgreSQL also provide something like a websocket where I can inject data faster? What if can't?

Can I use a cache design then? For example, my websocket give me new message every seconde. But I have a queue (or like a buffer pool) that store the message, I will then store the data from the queue, so that when websocket stopped sending message, my machine still works on injecting the data. I know that db could inject data fast, but I might need to process the data before I inject it, that is why I am thinking about a new design.

### Solution2

When dealing with high-frequency data input, especially from real-time sources like a WebSocket, and when there's a need to process and store this data in a relational database like PostgreSQL, there are several strategies to optimize and prevent data loss or excessive latency.

1. **Batch Inserts**:
   - Rather than inserting each message into the database one by one, you can accumulate messages into batches and then perform a single batch insert. Databases are generally more efficient at handling batch operations than individual record inserts.
2. **Caching & Buffering**:
   - As you mentioned, using a cache or buffer can be effective. You can store incoming messages in an in-memory structure (like a list in Python) or an in-memory database (like Redis). Once the buffer reaches a certain size or after a certain time interval, you can then process and batch insert the accumulated messages into PostgreSQL.
3. **Processing Optimization**:
   - If there's a need to process the data before inserting it, consider parallelizing this processing using threads or asynchronous tasks.
4. **Database Optimization**:
   - Make sure the PostgreSQL tables are properly indexed to handle the insert operations efficiently. Sometimes, disabling indexes during a massive insert and then rebuilding them afterward can be faster.
   - Consider using a connection pooler like `pgBouncer` to reduce the overhead of establishing new connections for each insert operation.
5. **Asynchronous Processing**:
   - Consider using task queues like `Celery` with a message broker like `RabbitMQ` or `Redis`. When a message arrives, place it in the task queue. Workers will then pick up these tasks and process and insert them into the database. This decouples the rate of message arrival from the rate of database insertion.
6. **Database Write-Ahead Log (WAL) Settings**:
   - Fine-tuning PostgreSQL's WAL settings can improve write performance. However, be cautious as changing these settings can also impact the durability guarantees of the database.

Here's a simple conceptual example using an in-memory buffer for batch inserts:

```python
import websocket
import json
import time

BUFFER_SIZE = 100  # Adjust as needed
buffer = []

def process_and_store_in_db(messages):
    # Process and batch insert the messages into PostgreSQL
    pass

def on_message(ws, message):
    global buffer
    buffer.append(message)
    if len(buffer) >= BUFFER_SIZE:
        process_and_store_in_db(buffer)
        buffer = []

# ... rest of the WebSocket code ...

# You might also want to set up a periodic flush to handle cases where the
# buffer doesn't reach BUFFER_SIZE within a certain time interval.
def periodic_flush():
    global buffer
    while True:
        if buffer:
            process_and_store_in_db(buffer)
            buffer = []
        time.sleep(10)  # Adjust the flush interval as needed

import threading
threading.Thread(target=periodic_flush).start()

```

This example keeps messages in an in-memory buffer. Once the buffer reaches a certain size (`BUFFER_SIZE`), it processes and batch inserts the messages into the database. There's also a periodic flush to handle cases where messages come in slowly and the buffer doesn't get filled quickly.

Remember that these are just strategies. Depending on the exact requirements, hardware, network conditions, and the volume of data, the ideal solution might be a combination of the above methods.
