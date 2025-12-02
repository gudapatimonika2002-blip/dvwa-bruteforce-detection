# Detection and Analysis of Brute-Force Login Attempts on a Local DVWA Instance

## 1. Overview of the Project
This project demonstrates how to simulate a brute-force login attack, detect it, and then analyze it on a vulnerable web application within a controlled lab environment.


We used **Damn Vulnerable Web Application (DVWA)** running on a local **XAMPP** stack and generated repeated login attempts against the DVWA login page. The resulting HTTP traffic was captured in **Apache access logs**, and a **detection timeline** was created to highlight suspicious login patterns.

The repository contains:

- `data/raw_logs/access.log` — Example Apache access log captured from a DVWA instance.
    This log contains all HTTP requests, including failed attempts.
- `data/processed/Detection Timeline (from access.log).xlsx` — a spreadsheet containing the extracted / analysed events and a timeline derived from the raw log.
- `screenshots/` — images of the DVWA login page and some examples failed attempts.
The main goal is to show how basic **log analysis** can reveal brute-force behavior, which is an important skill in cybersecurity and digital forensics.


---

## 2. Project Relevance

Brute-force login attacks are one of the most common and persistent threats against web applications. An attacker repeatedly tries different username/password combinations until one works. If such activity goes undetected, it can lead to:

- Unauthorized access to user accounts or admin panels  
- Compromise of sensitive data  
- Service disruption or account lockouts  
- Further lateral movement inside a network  

This project is relevant to:

- **Cybersecurity** – understanding how attacks appear in server logs and how to detect them  
- **Digital Forensics** – collecting, preservation, and interpretion of log-based evidence  
- **System/Network Administration** – learning how to monitor authentication activity and design defenses  

**Why we chose this topic:**

- It is hands-on but safe to perform in a local lab environment.  
- It connects directly to real-world attack techniques.  
- It helps to build practical skills in:
  - Working with vulnerable web apps (DVWA)  
  - Understanding HTTP requests and status codes  
  - Reading and interpreting Apache logs  
  - Building a simple detection timeline from raw data  

**Who can benefit from this project:**

- Students learning cybersecurity and digital forensics  
- Entry-level analysts learning how to read logs  
- System administrators interested in basic brute-force detection strategies  


---

## 3. Methodology

### 3.1 Environment Setup

**Tools and Environment:**

- **Operating System:** Windows  
- **Web Server Stack:** XAMPP (Apache + MySQL)  
- **Vulnerable Web Application:** DVWA (Damn Vulnerable Web Application)  
- **Browser:** Chrome  
- **Data Analysis:** Excel (for detection timeline)  
- **Version Control:** Git + GitHub  

**Steps:**

1. **Install XAMPP**  
   - Downloaded from ApacheFriends and installed on Windows.  
   - Started **Apache** and **MySQL** using the XAMPP Control Panel.

2. **Deploy DVWA in XAMPP**  
   - Downloaded the DVWA ZIP.  
   - Extracted and placed the folder into:
     ```text
     C:\xampp\htdocs\dvwa
     ```
   - Opened the setup page:
     ```text
     http://localhost/dvwa/setup.php
     ```

3. **Configure DVWA Database Connection**  
   - Copied `config/config.inc.php.dist` → `config/config.inc.php`.  
   - Updated database credentials to match XAMPP defaults:
     ```php
     $_DVWA['db_user']     = 'root';
     $_DVWA['db_password'] = '';
     ```
   - Clicked **“Create / Reset Database”** on the DVWA setup page.

4. **Log in to DVWA**  
   - Default credentials:
     - **Username:** `admin`  
     - **Password:** `password`  
   - Verified access to the DVWA dashboard.

5. **Set DVWA Security Level to LOW**  
   - Opened `DVWA Security` page.  
   - Changed security level to **LOW** and submitted.  
   - This allowed brute-force-style behavior without advanced protections.

---

### 3.2 Brute-Force Attack Simulation

The brute-force attack was done manually in a safe, controlled way:

1.  Firstly,Logged out of DVWA to return to the login page.  
2. Performed multiple login attempts with:
   - Correct username: `admin`  
   - Incorrect passwords (various values), for example:
     - `wrongpassword1`, `wrongpassword2`, `1233456`, `test1234`, etc.
3. Repeated **multiple POST requests** to `/dvwa/login.php` within short time intervals.
4. This generated a burst of **failed login attempts** that are visible as repeated `POST /dvwa/login.php` entries in the Apache access log.

Although the attack was not automated with a tool like Burp Suite or Hydra, the pattern of rapid, repeated login attempts still mimics a simple brute-force attack scenario and is sufficient for demonstrating detection through logs.

---

### 3.3 Data Collection

All evidence was collected from the local environment and stored in an organized folder structure.
The overall workflow can be summarized as:

[Local User] 
    ↓
[DVWA Login Page on localhost]
    ↓ (repeated POST /dvwa/login.php)
[Apache Web Server]
    ↓ (logs each request)
[Apache access.log]
    ↓
[Manual Review + Excel Timeline]
    ↓
[Detection of Brute-Force Patterns]
    ↓
[Findings + Defense Recommendations]

---

### 4. Results

### 4.1 Apache Access Log Evidence

A section of `access.log` showed repeated POST requests from the same IP (::1) within seconds


**Key observations:**

- `::1` = local machine  
- POST requests indicate login attempts  
- Status code **302** signals failed login redirection  
- Attempts clustered within seconds  

This behavior fits brute-force attack patterns.

---

### 4.2 Detection Timeline (timeline.csv)

Columns included:

- timestamp  
- ip_address  
- method  
- resource  
- status_code  
- classification  
- notes  

**Example Rows:**

| timestamp | ip_address | method | resource | status_code | classification | notes |
|----------|------------|---------|----------|-------------|----------------|-------|
| 2025-11-25 11:07:45 | ::1 | POST | /dvwa/login.php | 302 | Normal login | Login to dashboard |
| 2025-11-25 11:11:35 | ::1 | POST | /dvwa/login.php | 302 | Normal login | Another valid login |
| 2025-11-25 11:12:54 | ::1 | POST | /dvwa/login.php | 302 | Brute-force | Start of rapid attempts |
| 2025-11-25 11:13:13 | ::1 | POST | /dvwa/login.php | 302 | Brute-force | Short interval |
| 2025-11-25 11:13:22 | ::1 | POST | /dvwa/login.php | 302 | Brute-force | Continued burst |

This timeline separates:

- normal behavior  
- suspicious brute-force patterns  

---

### 4.3 Interpretation

The timeline and logs indicate:

- A cluster of login attempts occurred in seconds  
- All requests targeted `/dvwa/login.php`  
- All came from localhost (`::1`)  
- Pattern matches brute-force behavior  

Even basic logs offer clear forensic indicators including:

- frequency spikes  
- repeated POST attempts  
- identical IP and endpoint  

---

## 5. Defensive Measures and Recommendations

**Account Lockout Policy**  
Lock accounts after multiple failed attempts.

**CAPTCHA on Login Forms**  
Slows automated attacks.

**Rate Limiting / Throttling**  
Restricts login attempts per IP.

**Strong Password Policies**  
Reduces risk of password guessing.

**Logging & Monitoring**  
Track spikes in:

- failed logins  
- POST requests  
- repeated IP access  

**Multi-Factor Authentication (MFA)**  
Prevents takeover even if passwords are guessed.

---

## 6. Team Members and Contributions

### **Monika Gudapati**
- Installed and configured XAMPP & DVWA  
- Collected and organized logs & screenshots  
- Prepared final documentation  
- Managed GitHub repository  
- Co-presented the project  

### **Sanjana Reddy**
- Simulated brute-force login attempts  
- Analyzed Apache logs  
- Created detection timeline  
- Documented defensive strategies  
- Co-presented the project  

---

## 7. Conclusion

This project demonstrates how a simple brute-force attack can be:

- Safely simulated on DVWA  
- Logged by Apache  
- Detected through log and timeline analysis  

By analyzing repeated POST requests to `/dvwa/login.php` within seconds, we identified clear brute-force indicators.

The project reinforces important cybersecurity skills such as:

- log-based intrusion detection  
- authentication forensic analysis  
- web application security  
- understanding defense-in-depth  

These hands-on skills form a strong foundation for real-world cybersecurity and digital forensics work.



