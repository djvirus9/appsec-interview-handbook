# Vulnerable Snippets (Spot the Bug)

Each prompt is formatted for interviews: Question -> Hint -> Answer.

## Java

### 1) Insecure Deserialization

**Question**: What is the vulnerability in this code and how would you fix it?

```java
import java.io.*;

public class UserLoader {
    public Object load(byte[] payload) throws Exception {
        ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(payload));
        return ois.readObject();
    }
}
```

**Hint**: The input is untrusted and can control object graphs.

**Answer**:
- Vulnerability: Insecure deserialization.
- Why: `readObject()` on untrusted bytes can trigger gadget chains and code execution.
- Fix (use allowlist or safe format):

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

public class UserLoader {
    private final ObjectMapper mapper = new ObjectMapper()
        .disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);

    public User load(byte[] payload) throws Exception {
        // Use JSON with explicit types
        return mapper.readValue(payload, User.class);
    }
}
```

### 2) SQL Injection

**Question**: Identify the issue and remediate it.

```java
String sql = "SELECT * FROM users WHERE email = '" + email + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```

**Hint**: The query is constructed with user input.

**Answer**:
- Vulnerability: SQL injection.
- Why: String concatenation mixes code and data.
- Fix:

```java
String sql = "SELECT * FROM users WHERE email = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, email);
ResultSet rs = ps.executeQuery();
```

## Python

### 3) SSRF via URL Fetch

**Question**: What is the bug and how do you fix it?

```python
import requests

def fetch(url):
    return requests.get(url, timeout=5).text
```

**Hint**: The URL is provided by a user.

**Answer**:
- Vulnerability: SSRF.
- Why: Untrusted URLs can target internal services or metadata.
- Fix (allowlist + scheme check):

```python
import requests
from urllib.parse import urlparse

ALLOWED_HOSTS = {"example.com", "static.example.com"}


def fetch(url):
    parsed = urlparse(url)
    if parsed.scheme not in ("https",):
        raise ValueError("invalid scheme")
    if parsed.hostname not in ALLOWED_HOSTS:
        raise ValueError("host not allowed")
    return requests.get(url, timeout=5).text
```

### 4) Command Injection

**Question**: Identify the vulnerability.

```python
import os

def convert(path):
    return os.popen("convert " + path).read()
```

**Hint**: The input is concatenated into a shell command.

**Answer**:
- Vulnerability: OS command injection.
- Why: `path` can include shell metacharacters.
- Fix:

```python
import subprocess

def convert(path):
    return subprocess.check_output(["convert", path])
```

## C++

### 5) Buffer Overflow

**Question**: What is wrong here?

```cpp
char buf[16];
strcpy(buf, input);
```

**Hint**: Consider input length.

**Answer**:
- Vulnerability: Buffer overflow.
- Why: `strcpy` does not check bounds.
- Fix:

```cpp
char buf[16];
strncpy(buf, input, sizeof(buf) - 1);
buf[sizeof(buf) - 1] = '\0';
```

### 6) Use-After-Free

**Question**: Spot the bug.

```cpp
char* buf = (char*)malloc(32);
free(buf);
strcpy(buf, "test");
```

**Hint**: Memory is accessed after being freed.

**Answer**:
- Vulnerability: Use-after-free.
- Why: Writing to freed memory can lead to corruption or code execution.
- Fix:

```cpp
char* buf = (char*)malloc(32);
// use buf
strncpy(buf, "test", 31);
buf[31] = '\0';
free(buf);
buf = nullptr;
```

## JavaScript (Node.js)

### 7) Prototype Pollution

**Question**: What is the issue?

```javascript
app.post('/merge', (req, res) => {
  const out = Object.assign({}, req.body);
  res.json(out);
});
```

**Hint**: Merging untrusted objects can mutate prototypes.

**Answer**:
- Vulnerability: Prototype pollution.
- Why: `__proto__` or `constructor` can be injected.
- Fix:

```javascript
const safeCopy = (obj) => {
  const out = {};
  for (const [k, v] of Object.entries(obj)) {
    if (k === '__proto__' || k === 'constructor' || k === 'prototype') continue;
    out[k] = v;
  }
  return out;
};

app.post('/merge', (req, res) => {
  const out = safeCopy(req.body);
  res.json(out);
});
```

### 8) Path Traversal

**Question**: Identify the vulnerability.

```javascript
app.get('/file', (req, res) => {
  const file = req.query.name;
  res.sendFile('/var/app/files/' + file);
});
```

**Hint**: User-controlled path is appended directly.

**Answer**:
- Vulnerability: Path traversal.
- Why: `../` can escape the base directory.
- Fix:

```javascript
const path = require('path');

app.get('/file', (req, res) => {
  const file = req.query.name || '';
  const safePath = path.normalize(file).replace(/^\.\.(\/|\\)/, '');
  res.sendFile(path.join('/var/app/files', safePath));
});
```
