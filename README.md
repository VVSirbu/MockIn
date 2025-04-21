# MockIn

**MockIn** is a high-performance, cross-protocol mock server for local development, testing, and automation.  
Supports HTTP and TCP protocols with flexible request matching and a powerful control API.

---

## ✨ Features

- ✅ Supports both **HTTP** and **TCP**
- ✅ Multiple mocks per port
- ✅ Advanced matching:
    - Method + Path
    - Query params
    - Headers
    - Body content (exact, regex, contains, startsWith, endsWith)
    - JSONPath
- ✅ Default fallback mocks
- ✅ Control API on port `9000`
- ✅ Request/response logging
- ✅ Dynamic add/remove mocks

---

## 🚀 Quick Start

```bash
java -jar mockin-cli-1.0-SNAPSHOT-jar-with-dependencies.jar
```

This starts the Control API at `http://localhost:9000`  
and automatically boots HTTP/TCP servers based on mocks.

---

## 🧩 Sample `mocks.json`

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
      "jsonPath": {
        "$.user.email": "test@example.com"
      }
    },
    "response": {
      "status": 200,
      "body": "valid json"
    }
  },
  {
    "protocol": "tcp",
    "port": 5555,
    "request": {
      "rawContains": "HELLO"
    },
    "response": {
      "raw": "hello received"
    }
  },
  {
    "protocol": "tcp",
    "port": 5555,
    "request": {
      "bodyRegex": "^\\d{4}$"
    },
    "response": {
      "raw": "4-digit number accepted"
    }
  }
]
```

---

## 📡 Control API (port `9000`)

| Method | Endpoint                            | Description                             |
|--------|-------------------------------------|-----------------------------------------|
| POST   | `/api/mocks`                        | Add a single mock (JSON)                |
| POST   | `/api/mocks/upload`                 | Upload mocks.json (multipart/form)      |
| GET    | `/api/status`                       | List running mock servers               |
| GET    | `/api/logs`                         | View recent request/response logs       |
| DELETE | `/api/mocks`                        | Delete **all mocks** and stop servers   |
| DELETE | `/api/mocks/single`                 | Delete specific mock by match params    |
| DELETE | `/api/mocks/port/{port}?protocol=`  | Remove all mocks for a given port       |
| DELETE | `/api/mocks/preserve-default?port=...&protocol=...` | Remove all mocks except `isDefault` ones |

---

## 📥 Examples

### Upload mocks:
```bash
curl -X POST http://localhost:9000/api/mocks/upload      -F "file=@mocks.json"
```

### Add single mock:
```bash
curl -X POST http://localhost:9000/api/mocks      -H "Content-Type: application/json"      -d '{"protocol":"http","port":8080,"request":{"method":"GET","path":"/ping"},"response":{"status":200,"body":"pong"}}'
```

### Delete all mocks:
```bash
curl -X DELETE http://localhost:9000/api/mocks
```

### Delete mocks from port:
```bash
curl -X DELETE "http://localhost:9000/api/mocks/port/8080?protocol=http"
```

### Preserve only default mocks:
```bash
curl -X DELETE "http://localhost:9000/api/mocks/preserve-default?port=8080&protocol=http"
```

### Delete a specific mock:
```bash
curl -X DELETE http://localhost:9000/api/mocks/single      -H "Content-Type: application/json"      -d '{"port":8080,"protocol":"http","match":{"method":"POST","path":"/submit"}}'
```

---

## 🧾 Log Format

```json
[
  {
    "protocol": "http",
    "port": 8080,
    "method": "POST",
    "pathOrBody": "/submit",
    "matched": true,
    "responseCode": 200,
    "responseBody": "email accepted",
    "durationMs": 5,
    "timestamp": "2025-04-21T12:00:00Z"
  }
]
```

---

## 📌 Notes

- Supports `.raw`, `.rawContains`, `.rawStartsWith`, `.rawEndsWith`, `.bodyRegex`, `.jsonPath`, `.isDefault`
- If no mock matches, and `isDefault: true` is present — it will be used as a fallback
- TCP mocks match line-by-line messages

---

## 🛣️ Roadmap v1.1+

- [ ] Log filtering by date, port, protocol
- [ ] Export logs as JSON or CSV
- [ ] Schema validation for mock requests
- [ ] UI dashboard for managing mocks
- [ ] WebSocket, UDP, gRPC support

---

## 👨‍💻 Author

Built by **Valentin S.** with ❤️ using Java + Netty  
For developers who need control over their local services.

---