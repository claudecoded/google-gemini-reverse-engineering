# 🕵️‍♂️ Inside Google AI: Reverse Engineering the Gemini Web Interface (made by an human)

This repository is a technical study focused on uncovering what happens "behind the scenes" of the Google Gemini web interface. Here, I document how the browser communicates with Google's AI cloud servers using browser Developer Tools (DevTools).

---

## 🛠️ How to Inspect the AI "Thinking"

While the core AI inference occurs remotely on Google's cloud servers, we can intercept and analyze the real-time data exchange using the browser's **Network tab** (Firefox/Chrome):

1. Open Google Gemini and press `F12` (or right-click and select **Inspect**).
2. Navigate to the **Network** tab.
3. Type a prompt and send a message to the AI.
4. Watch the real-time HTTP requests populate the list.

<img width="1093" height="605" alt="Screenshot 2026-10-01 140833" src="https://github.com/user-attachments/assets/caebc140-f500-4460-8ed0-6d2af3f10161" />



https://github.com/user-attachments/assets/3297fe95-fb31-4aea-b3b0-5a030cdccd14


---

## 🔬 Technical Discoveries

By analyzing the network traffic, we can break down three core pillars of the application's infrastructure:

### 1. The `batchexecute` Protocol
Google does not use a standard REST API with clean, readable JSON keys. Instead, web actions are bundled and routed through a central endpoint called `/batchexecute`.
* **XSSI Protection:** Every server response prefixes raw data with a unique string: `)]}'\n\n`. This cross-site script inclusion defense prevents malicious external sites from reading your conversation history.
* **Nested Arrays:** Your prompt data, account state, and AI operational parameters are transmitted inside massive, obfuscated nested arrays (e.g., `[[["Tp8gic","[...]",null,"generic"]]]`), bypassing traditional key-value pairs.

### 2. Real-Time Incremental Output (HTTP Streaming)
The Gemini UI renders responses word-by-word as they generate. On the network side, this relies on **HTTP Data Streaming**. The connection remains open, and the server pushes text fragments (*chunks*) in rapid millisecond bursts, allowing for progressive rendering.

### 3. Authentication & State Management
To securely authenticate requests and sync chats with your specific Google Account, the browser appends highly encrypted, high-priority cookies to every header payload, primarily:
* `__Secure-1PSID`
* `__Secure-1PSIDTS`

---

## 📂 Repository Structure

Feel free to contribute or adapt this structure for your experiments:
* `/docs` -> Annotated screenshots of the Network tab mapping out request/response sequences.
* `/payloads` -> Sanitized raw logs captured from the `batchexecute` endpoint (always remove your personal cookies before sharing!).

---

## 🛑 Disclaimer
This project is strictly for educational purposes and architectural analysis. Google's internal API structures, endpoints, and parameters change regularly without prior notification.
