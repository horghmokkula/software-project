# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at emmi.vinberg@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

Storing sensitive information like SSN in plain text in the database is a security risk. This data should be encrypted or hashed.

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

Currently sensitive information such as Social Security Numbers (SSN) are stored as plain text in the database. This is a high risk, as an attacker has the capability to gain access to the database, therefore have direct access to the sensitive data.

Incriminated source code:
```python
# Example of storing SSN in plain text
cursor.execute('''CREATE TABLE IF NOT EXISTS students (
                    student_number TEXT PRIMARY KEY,
                    name TEXT,
                    contact TEXT,
                    ssn TEXT,
                    image_path TEXT
                )''')

# Inserting plain-text SSN
cursor.execute("INSERT INTO students (student_number, name, contact, ssn, image_path) VALUES (?, ?, ?, ?, ?)",
               (student_number, name, contact, ssn, image_path))
conn.commit()
```

## PoC

Steps to Reproduce
1. Clone the repository locally:
   ```bash
   git clone https://github.com/horghmokkula/software-project.git
   cd software-project
   ```
2. Set up the database and run the application:
   ```bash
   python main.py
   ```
3. Use the application to add a student, providing an SSN as input.
4. Open the SQLite database file (`students.db`) using any SQLite browser or tool.
5. Query the `students` table to retrieve the SSN:
   ```sql
   SELECT ssn FROM students;
   ```
6. Observe that the SSN is stored in plain text.

## Impact

Sotring sensitive data in plain text is serious issue with multiple implications. For example data breach risk: attackers can gain direct access to sensitive info in case of database being compromised. Privacy violations are another consideration; as the sensitive data can be exposed, it can be used for identity theft and other malicious ways. It is also non-compliant with many jurisdictions such as GDPR and HIPAA.

## Remediation

Suggested Fix
1. **Encrypt Sensitive Data**: Use a strong encryption algorithm (e.g., AES-256) to encrypt the SSN before storing it in the database.
   ```python
   from cryptography.fernet import Fernet

   # Generate a key and initialize Fernet
   key = Fernet.generate_key()
   cipher_suite = Fernet(key)

   # Example of encrypting SSN
   encrypted_ssn = cipher_suite.encrypt(ssn.encode('utf-8'))

   # Storing encrypted SSN
   cursor.execute("INSERT INTO students (student_number, name, contact, ssn, image_path) VALUES (?, ?, ?, ?, ?)",
                  (student_number, name, contact, encrypted_ssn, image_path))
   conn.commit()
   ```

2. **Secure Key Management**: Store the encryption key securely, separate from the database. Use environment variables or secret management tools.

3. **Ensure Secure Transmission**: Use TLS/SSL for all communication between the application and the database.

4. **Regularly Audit Data Access**: Implement logging and monitoring to detect unauthorized access.

These measures will help protect sensitive user information and ensure compliance with data protection standards.

## GitHub Security Advisories

If possible, please could you create a private [GitHub Security Advisory](https://help.github.com/en/github/managing-security-vulnerabilities/creating-a-security-advisory) for these findings? This allows you to invite me to collaborate and further discuss these findings in private before they are [published](https://help.github.com/en/github/managing-security-vulnerabilities/publishing-a-security-advisory). I will be happy to collaborate with you, and review your fix to make sure that all corner cases are covered. 
When you use a GitHub Security Advisory, you can request a CVE identification number from GitHub. GitHub usually reviews the request within 72 hours, and the CVE details will be published after you make your security advisory public. Publishing a GitHub Security Advisory and a CVE will help notify the downstream consumers of your project, so they can update to the fixed version.

## Credit

Emmi Vinberg and GitHub Copilot

## Contact

emmi.vinberg@student.laurea.fi

## Disclosure Policy

*Describe or link to your disclosure policy. It's important to have a disclosure policy where the public disclosure deadline, and the potential exceptions to it, are clear. You are free to use the [GitHub Security Lab disclosure policy](https://securitylab.github.com/advisories/#policy), which is copied below for your convenience, if it resonates with you.*

The *your_team_name_here* research team is dedicated to working closely with the open source community and with projects that are affected by a vulnerability, in order to protect users and ensure a coordinated disclosure. When we identify a vulnerability in a project, we will report it by contacting the publicly-listed security contact for the project if one exists; otherwise we will attempt to contact the project maintainers directly.

If the project team responds and agrees the issue poses a security risk, we will work with the project security team or maintainers to communicate the vulnerability in detail, and agree on the process for public disclosure. Responsibility for developing and releasing a patch lies firmly with the project team, though we aim to facilitate this by providing detailed information about the vulnerability.

Our disclosure deadline for publicly disclosing a vulnerability is: 90 days after the first report to the project team.

We **appreciate the hard work** maintainers put into fixing vulnerabilities and understand that sometimes more time is required to properly address an issue. We want project maintainers to succeed and because of that we are always open to discuss our disclosure policy to fit your specific requirements, when warranted.