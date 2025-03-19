# **Objective**  
Run the Metasploit module `auxiliary/scanner/http/http_put` against a target website while routing traffic through Burp Suite. Identify the **last line** of the HTTP request sent by the module.

---

## **Solution Walkthrough**

### 1. **Start Burp Suite**  
- Open Burp Suite and ensure the proxy listener is active on `127.0.0.1:8080` (default settings).  
- Go to **Proxy → Intercept** and ensure interception is **OFF** (to avoid blocking requests).

### 2. **Launch Metasploit**  
Open a terminal and start `msfconsole`:  
```bash
msfconsole
```

### 3. **Load the HTTP PUT Module**  
Use the `http_put` scanner to test if the target allows HTTP PUT requests:  
```msf
use auxiliary/scanner/http/http_put
```

### 4. **Set Up Proxy Routing**  
Configure Metasploit to route traffic through Burp:  
```msf
set PROXIES HTTP:127.0.0.1:8080
```

### 5. **Configure Target Details**  
- Set the target IP and port (replace values with your lab details):  
  ```msf
  set RHOSTS 94.237.56.224
  set RPORT 35060
  ```  
- **Optional**: Specify a path (e.g., `/uploads`). If skipped, Metasploit uses a random filename by default.  

### 6. **Run the Module**  
Execute the scan:  
```msf
run
```

### 7. **Analyze Traffic in Burp**  
- Go to **Proxy → HTTP History** in Burp.  
- Locate the `PUT` request sent to your target (e.g., `94.237.56.224:35060`).  
- Inspect the **raw request** to see its structure.  

---

## **Example Request in Burp**  
The `http_put` module sends a `PUT` request with a test payload. Here’s the structure:  
```
PUT /random_filename.txt HTTP/1.1
Host: 94.237.56.224:35060
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Content-Length: 5

msf test file
```
![f187e258faf7e289587e12e532eef4d](https://github.com/user-attachments/assets/619186d0-ae23-4809-9bbb-5ea1a0b5eccf)
---

## **Key Observations**  
- The **last line** of the request is `msf test file`, which is the default payload sent by the module.  
- If you skipped setting the `PATH`, Metasploit auto-generates a random filename (e.g., `/xyz.txt`), but the payload (`test`) remains unchanged.  
- The `Content-Length: 5` reflects the length of `test` (including a newline character).

---

## **Final Answer**  
The last line of the HTTP request sent by the `http_put` module is:  
**`msf test file`**



![2a6a57b27b248ba19eea2e0d74bea85](https://github.com/user-attachments/assets/e3e4b549-09e1-4529-a8e6-c7a6b47079ec)

---

## **Why This Works**  
- The module tests if the server allows HTTP PUT requests by uploading a dummy file with the content `test`.  
- Even if you omit the `PATH` parameter, Metasploit uses a random filename, but the payload (`test`) is hardcoded into the module.  
- Burp Suite captures the raw request, allowing you to inspect the exact bytes sent to the server.  

😊
