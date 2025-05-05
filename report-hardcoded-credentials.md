# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at emmi.vinberg@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

The default username and password (`admin`, `password`) are hard-coded in the database. This is insecure and can be exploited if not changed.

## Product

Software Project

1.0? (Version information unavailabe)

## Details

Vulnerable code

# Add a default username and password (note that this is just an example)
cursor.execute("INSERT INTO users (username, password) VALUES (?, ?)", ("admin", "password"))
conn.commit()

Hard-coding credentials allows attackers access to the source code. Binaries can also find these and exploit them. Unauthorised access to sensitive data or systems is more likely in a case where default credentials are predictable.

## PoC

1. Clone the repository locally:
   ```bash
   git clone https://github.com/horghmokkula/software-project.git
   cd software-project
   ```
2. Open the `main.py` file and locate the following code:
   ```python
   cursor.execute("INSERT INTO users (username, password) VALUES (?, ?)", ("admin", "password"))
   conn.commit()
   ```
3. Run the application and attempt to log in using the hard-coded credentials:
   ```bash
   python main.py
   ```
   - Username: `admin`
   - Password: `password`
4. Observe that access is granted without any additional security checks.

## Impact

This vulnerability allows unauthorised administrative access to the application by exploiting the predictable credentials. This can lead to sensitive data being leaked and accesses unauthorised, database records being manipulated or deleted as well as losing control of the application environment.

## Remediation

Suggested fix is to remove the hard-coded credentials from the main.py file. To ensure secure management of credentials, it is useful to implement and secure system for storing credentials:

1. Prompt the user to set up an initial administrator account during application setup.
2. Use environment variables or configuration files to securely store credentials.
3. Hash passwords before storing them in the database using a strong hashing algorithm like bcrypt:
   ```python
   import bcrypt

   hashed_password = bcrypt.hashpw("password".encode('utf-8'), bcrypt.gensalt())
   cursor.execute("INSERT INTO users (username, password) VALUES (?, ?)", ("admin", hashed_password))
   conn.commit()

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