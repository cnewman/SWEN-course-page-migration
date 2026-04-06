---
title: 'DVWA Web Application Security'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## DVWA Web Application Security Activity

This activity introduces you to web application vulnerabilities using the Damn Vulnerable Web Application (DVWA). You will practice identifying and exploiting common vulnerabilities in a controlled testbed environment.

---

## Setup

Choose one of the following installation methods:

### Option 1: Windows Portable Zip (Recommended)

**Best for:** SE labs or Windows devices. Simplest and most reliable.

1. Download the XAMPP portable installation (~250MB) from myCourses under "Content > DVWA Web App"
2. Extract using 7-zip to `c:/xampp` (avoid spaces in the path)
3. Navigate to the unzipped directory and run `setup-xampp.bat`, selecting option 1 to refresh when prompted
4. Start servers via either:
   - Running `apache_start.bat` and `mysql_start.bat` separately, or
   - Using `xampp_start.exe` / `xampp_stop.exe` from the Command Prompt
5. **Important:** When prompted for administrator privileges, select "Cancel" — do not allow firewall access, as this would expose your machine as a network server

### Option 2: RLES (Remote Lab)

**Best for:** Mac/Linux users without a local Windows machine. Requires reliable internet.

1. Sign into [https://rlescloud.rit.edu/](https://rlescloud.rit.edu/) with your RIT credentials
2. Request a Windows 10 machine on the NAT network
3. Wait for provisioning (several minutes)
4. Click the gear icon on the Deployments page and select "Connect to Remote Console" (check browser pop-up blockers if nothing happens)
5. Log in with username `Student` and password `student`
6. Download `xampp-portable.zip` from myCourses and follow the Windows Portable Zip steps above

### Option 3: Docker

**Best for:** Students already familiar with Docker.

Note: Docker is straightforward when it works, but debugging Docker issues can be complex and it may conflict with other virtualization software.

### Option 4: Manual Installation

Install Apache, MySQL, and DVWA independently using these resources:

- [Past student's Linux installation instructions](https://github.com/meyersbs/linux-dvwa-instructions)
- [Original DVWA repository](https://github.com/ethicalhack3r/DVWA)

---

## Activity

Once your environment is running, navigate to [http://127.0.0.1](http://127.0.0.1) and log into DVWA. Use the Setup page to initialize the database, then set the security level to **"low"** to begin.

Document your findings in a Google Doc as you work through each part below.

---

### Part 1: SQL Injection

Construct a SQL injection exploit that returns all usernames from the database.

Answer the following questions in your document:

1. As a *tester*, how would you know that a potential SQL injection vulnerability exists?
2. As a *code reviewer*, what should you be looking for to avoid SQL injection?
3. Construct an exploit that does more than just list usernames (e.g., extract password hashes or determine the database schema).
4. Switch DVWA to "medium" security. Does your exploit still work? Explain.
5. Take a look at the solution for "high" security. Under what conditions might SQL injection still work?
6. Review your own project code. Identify at least one place where a SQL injection vulnerability could exist, and explain why — or explain how your code already prevents it (e.g., parameterized queries, ORM).

---

### Part 2: Cross-Site Scripting (XSS)

Navigate to the **XSS (Reflected)** section of DVWA and answer questions 1–5, then switch to the **XSS (Stored)** section for question 6.

Answer the following questions in your document:

1. How might a tester recognize an XSS vulnerability?
2. Use the Developer Tools in your browser to modify the maximum length of the input field. Why does this work? How might you prevent larger inputs if you were the developer?
3. Switch the security level to "medium". Construct an XSS exploit that will work under "medium" security.
4. Construct a phishing exploit that creates an extra form on the page that submits to an external site.
5. Switch to the **XSS (Stored)** section. Inject a payload and observe what happens when another user visits the page. How does stored XSS differ from reflected XSS in terms of who is affected and how an attacker would exploit it?
6. Review your own project code. Identify at least one place where an XSS vulnerability could exist, and explain why — or explain how your code already prevents it (e.g., output encoding, framework escaping, Content Security Policy).

---

### Part 3: Command Execution

Navigate to the **Command Execution** section of DVWA and construct a working exploit.

Answer the following questions in your document:

1. What input did you use to execute an arbitrary command? What does it reveal about the underlying implementation?
2. As a *tester*, how would you recognize that a feature might be vulnerable to command injection?
3. As a *code reviewer*, what patterns in the code would indicate a command injection risk?
4. Compare command injection to SQL injection and XSS in terms of severity. Which would you consider most dangerous and why? Consider what resources each vulnerability gives an attacker access to.
5. Review your own project code. Does any part of it invoke system commands, shell processes, or external programs with user-supplied input? Explain why a vulnerability does or does not exist.

---

### Bonus: bWAPP

If you finish early, explore [bWAPP](http://www.itsecgames.com/), another intentionally vulnerable web application with additional vulnerability types to practice.

---

## Submission

Convert your Google Document to MS Office or PDF format and submit to myCourses under the "Security" assignment.
