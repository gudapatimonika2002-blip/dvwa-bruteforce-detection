# Detection and Analysis of Brute-Force Login Attempts on a Local DVWA Instance

## 1. Project Overview

This project demonstrates how a brute-force login attack can be **simulated, detected, and analyzed** on a vulnerable web application in a controlled lab environment.

We used **Damn Vulnerable Web Application (DVWA)** running on a local **XAMPP** stack and generated repeated login attempts against the DVWA login page. The resulting HTTP traffic was captured in **Apache access logs**, and a **detection timeline** was created to highlight suspicious login patterns.

The repository includes:

- `data/raw_logs/access.log` — example Apache access log captured from a DVWA instance.
- `data/processed/Detection Timeline (from access.log).xlsx` — a spreadsheet showing extracted / analyzed events and a timeline derived from the raw log.
- `screenshots/` — images showing the DVWA login page and example failed attempts.
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
- **Digital Forensics** – collecting, preserving, and interpreting log-based evidence  
- **System/Network Administration** – learning how to monitor authentication activity and design defenses  

**Why we chose this topic:**

- It is hands-on but safe to perform in a local lab environment.  
- It connects directly to real-world attack techniques.  
- It helps build practical skills in:
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

The brute-force behavior was simulated manually in a safe, controlled way:

1. Logged out of DVWA to return to the login page.  
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







