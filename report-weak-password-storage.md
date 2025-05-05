# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at emmi.vinberg@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

Passwords are stored in plain text in the `users` table. This is a critical vulnerability. Passwords should be hashed using a secure algorithm like bcrypt or Argon2.

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

Passwords are stored as plain text. This is acritical vulnerability, as in case the database is compromised, attackers will be able to access all user credentials directly. Passwords should always be hashed with a secure algorithm such as bcrypt or Argon2.

Incriminated source code in `main.py`:
```python
# Add a default username and password (note that this is just an example)
cursor.execute("INSERT INTO users (username, password) VALUES (?, ?)", ("admin", "password"))
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
3. Open the SQLite database file (`students.db`) using any SQLite browser or tool.
4. Query the `users` table to retrieve the password:
   ```sql
   SELECT username, password FROM users;
   ```
5. Observe that the password is stored in plain text.

## Impact

Storing passwords as plain text is a severe issue that poses multiple risks. If the database is comproimsed, attackers will have immediate access to all user passwords, enabling credential theft. As many users use same passwords across multiple platforms, storing passwords as plain text can lead to bbreaches in other applications. This is also non-compliant with many data protections laws such as GDPR and PCI DSS. Unsecure storage of passwords can also lead to heavy penalties for the company.

## Remediation

Suggested Fix
1. **Hash Passwords**: Use a robust hashing algorithm like bcrypt or Argon2 to hash passwords before storing them in the database.
   Example using bcrypt:
   ```python
   import bcrypt

   # Hash the password
   password = "password"
   hashed_password = bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())

   # Store the hashed password
   cursor.execute("INSERT INTO users (username, password) VALUES (?, ?)", ("admin", hashed_password))
   conn.commit()
   ```

2. **Verify Passwords**: Use the same hashing algorithm to compare the input password during login.
   Example:
   ```python
   def login():
       username = input("Enter username: ")
       password = input("Enter password: ")
       
       cursor.execute("SELECT password FROM users WHERE username = ?", (username,))
       result = cursor.fetchone()
       
       if result and bcrypt.checkpw(password.encode('utf-8'), result[0]):
           print("Login successful.")
       else:
           print("Incorrect username or password.")
   ```

3. **Enforce Strong Password Policies**: Ensure that users create strong passwords (minimum length, mix of characters, etc.).

These changes will significantly improve the security of user credentials and reduce the likelihood of a successful attack. 

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