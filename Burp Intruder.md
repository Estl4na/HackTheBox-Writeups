### **Complete Write-Up: Using Burp Intruder to Fuzz for `.html` Files Under `/admin`**

---

#### **Objective**
The goal is to use **Burp Intruder** in the **Community Edition** to fuzz for `.html` files under the `/admin` directory of the target web application (`http://94.237.55.112:55183/admin/`). The task involves finding a file containing the flag.

---

### **Step 1: Set Up Burp Suite and Browser**
1. **Start Burp Suite**:
   - Launch **Burp Suite Community Edition**.
   - Ensure the proxy listener is running on the default port `8080`.

2. **Configure the Browser**:
   - Use the pre-configured browser that comes with Burp Suite or configure your browser to use Burp as a proxy.
   - Set the proxy settings in your browser to `127.0.0.1:8080`.

3. **Visit the Target Web Application**:
   - In the browser, navigate to the target URL: `http://94.237.55.112:55183/admin/`.

---

### **Step 2: Capture the Request in Burp Proxy**
1. **Intercept the Request**:
   - Go to the **Proxy** tab in Burp Suite and ensure "Intercept is on".
   - Refresh the page in your browser (`http://94.237.55.112:55183/admin/`).
   - The request will be captured in Burp's **Proxy > HTTP history**.

2. **Send the Request to Intruder**:
   - Locate the intercepted request in the **HTTP history**.
   - Right-click on the request and select **"Send to Intruder"**, or use the shortcut `[CTRL+I]`.

---

### **Step 3: Configure Burp Intruder**
1. **Go to Intruder**:
   - Click on the **Intruder** tab or use the shortcut `[CTRL+SHIFT+I]`.

2. **Set the Target**:
   - In the **Target** tab, verify the target details:
     - Host: `94.237.55.112`
     - Port: `55183`
     - Protocol: `http`

3. **Define Payload Positions**:
   - Go to the **Positions** tab.
   - Modify the request path to include the payload position for fuzzing `.html` files under `/admin`.
   - Replace the placeholder `DIRECTORY` in the path `/admin/DIRECTORY` with `§DIRECTORY§` to mark it as the payload position.
     - Example: `GET /admin/§DIRECTORY§.html HTTP/1.1`
   - Ensure the **Attack Type** is set to **Sniper** (default).

4. **Load the Wordlist**:
   - Go to the **Payloads** tab.
   - Select **Payload Set**: `1`.
   - Choose **Payload Type**: `Simple List`.
   - Click **Load** and select the wordlist file `/opt/useful/seclists/Discovery/Web-Content/common.txt`.
  ![image](https://github.com/user-attachments/assets/a7a157e3-8263-4d38-a9fe-33edb4c5a026)


5. **Add File Extension**:
   - Go to the **Payload Processing** section.
   - Click **Add** and select **Add suffix**.
   - Enter `.html` as the suffix to append `.html` to each word in the wordlist.

6. **Filter Out Unwanted Entries**:
   - Add a rule to skip lines starting with `.`:
     - Click **Add** and select **Skip if matches regex**.
     - Enter the regex pattern: `^\..*$`.

7. **Enable Payload Encoding**:
   - Ensure **URL-encoding** is enabled.

---

### **Step 4: Customize Attack Options**
1. **Go to the Options Tab**:
   - Under **Grep - Match**, enable the option to flag responses with specific patterns.
   - Clear the existing list and add `200 OK` to match successful responses.
   - Disable **Exclude HTTP Headers** to ensure headers are included in the match.

2. **Set Resource Pool (Optional)**:
   - If you want to control network resources, go to the **Resource Pool** tab and adjust settings. For this task, leave it at default.

---

### **Step 5: Start the Attack**
1. **Launch the Attack**:
   - Click the **Start Attack** button.
   - Wait for the attack to complete. Note that the free Community Edition of Burp Intruder is throttled to 1 request per second, so this may take some time.

2. **Analyze Results**:
   - Once the attack finishes, look for responses with the HTTP status code `200 OK`.
   - Sort the results by the `200 OK` column to bring successful hits to the top.
   - Identify the `.html` file(s) under `/admin` that returned a `200 OK` response.

---

### **Step 6: Verify the Flag**
1. **Manually Visit the File**:
   - Open the browser and navigate to the discovered `.html` file under `/admin`.
     - Example: `http://94.237.55.112:55183/admin/filename.html`.

2. **Locate the Flag**:
   - Check the content of the file for the flag.
   - The flag will typically be in a recognizable format, such as `HTB{...}`.

---

### **Final Answer**
Once you locate the flag, submit it to complete the challenge.

![image](https://github.com/user-attachments/assets/aafd5404-4d17-4549-b196-6ae6365b9051)


---

### **Key Notes**
- **Throttling in Community Edition**: The free version of Burp Intruder is limited to 1 request per second, making it slower than CLI-based tools like `ffuf` or `gobuster`. However, it is still effective for smaller tasks like this one.
- **Wordlist Selection**: The wordlist `/opt/useful/seclists/Discovery/Web-Content/common.txt` is a widely used resource for web content discovery. It contains common file and directory names.
- **Regex Filtering**: Skipping lines starting with `.` ensures that hidden files (e.g., `.htaccess`) are excluded from the fuzzing process, reducing noise in the results.

---

By following these steps, you should successfully identify the `.html` file containing the flag under the `/admin` directory. 
