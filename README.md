# MockIn

**MockIn** is a high-performance, cross-protocol mock server for development, testing, and simulating APIs or raw socket services.

Features:
-  Supports HTTP and TCP protocols
-  Multiple ports
-  Flexible matching: path, query, headers, body, regex
-  Control API for managing mocks
-  Full request/response logging

---

##  Quick Start

###  Download and run:

```bash
java -jar mockin-cli-1.0-SNAPSHOT-jar-with-dependencies.jar
```

MockIn will automatically start:
- Port **9000** as the Control API
- And any ports declared in your mock definitions (HTTP/TCP)

---

##  Sample `mocks.json`

```json
[
  {
    "protocol": "http",
    "port": 8080,
    "request": {
      "method": "GET",
      "path": "/ping"
    },
    "response": {
      "status": 200,
      "body": "pong"
    }
  },
  {
    "protocol": "http",
    "port": 8080,
    "request": {
      "method": "POST",
      "path": "/submit",
      "bodyRegex": ".*\"email\"\s*:\s*\"[^\"]+\".*"
    },
    "response": {
      "status": 200,
      "body": "email accepted"
    }
  },
  {
    "protocol": "tcp",
    "port": 5555,
    "request": {
      "bodyRegex": "^\\d{5}$"
    },
    "response": {
      "raw": "number accepted"
    }
  }
]
```

---

##  Control API (port 9000)

| Method | Path                 | Description                                |
|--------|----------------------|--------------------------------------------|
| POST   | `/api/mocks`         | Add a single mock                          |
| POST   | `/api/mocks/upload`  | Upload `mocks.json` (multipart/form)       |
| DELETE | `/api/mocks`         | Remove all mocks and stop servers          |
| GET    | `/api/status`        | Get list of active mock servers            |
| GET    | `/api/logs`          | View request/response logs                 |

---

##  `curl` Examples

###  Upload mocks:
```bash
curl -X POST http://localhost:9000/api/mocks/upload   -F "file=@mocks.json"
```

###  Check status:
```bash
curl http://localhost:9000/api/status
```

### 🗑 Clear all mocks:
```bash
curl -X DELETE http://localhost:9000/api/mocks
```

---

##  Log Format

```json
[
  {
    "protocol": "http",
    "port": 8080,
    "method": "GET",
    "pathOrBody": "/ping",
    "matched": true,
    "responseCode": 200,
    "responseBody": "pong",
    "durationMs": 3,
    "timestamp": "2025-04-19T21:45:10Z"
  }
]
```

---

##  Roadmap for v1.1+

- [ ] Log filtering by protocol, port, date
- [ ] Log export to file
- [ ] Extended matchers: `startsWith`, `contains`, JSON path
- [ ] Contract schema validation
- [ ] UI dashboard for mock management
- [ ] WebSocket, UDP, gRPC protocol support

---

##  Author

MockIn was built by ValentinS as a lightweight, extensible platform to kickstart more advanced developer tools.

Built with Java + Netty. Fast, flexible, and designed for real-world automation.