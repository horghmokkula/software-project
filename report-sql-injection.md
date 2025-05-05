# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at emmi.vinberg@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

The `login` function uses user input directly in SQL queries without proper sanitation. Although parameterized queries are used, the input is not validated, which could still lead to potential issues if the database driver is not robust.

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

The login function uses user input in SQL queries without proper input validation. The absense can lead to issues if the database driver does not accurately handle special characters or unexpected input formats.

Incriminated source code:

```python
def login():
    while True:
        username = input("Enter username: ")
        password = input("Enter password: ")
        
        cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", (username, password))
        user = cursor.fetchone()
        
        if user:
            print("Login successful.")
            break
        else:
            print("Incorrect username or password. Please try again.")
```

Main issue is that the input values for username and password are not validated prior to being passed on to cursor.execute() function. This makes it vulnerable for attackers to exploit this by inputting malicious inputs to exploit the database or underlying system.

## PoC

Steps to Reproduce
1. Clone the repository locally:
   ```bash
   git clone https://github.com/horghmokkula/software-project.git
   cd software-project
   ```
2. Set up the database and start the application:
   ```bash
   python main.py
   ```
3. During the login process, use the following input to simulate a potential attack:
   - Username: `admin`
   - Password: `' OR '1'='1`

   This input could potentially bypass the authentication mechanism depending on the robustness of the database driver, allowing unauthorized access.

## Impact

The lack of input validation could lead to a few different issues. An example of this would be SQL injection risk: While parameterised queries reduce most SQL injection attacks, lack of input validation still leaves the application open to exploits, if the database driver is incapable of handling certain edge cases. Another example is security breach: unauthorised user can gain access to the application, therefore accessing sensitive data and compromising the system.

## Remediation

Suggested Fix
1. **Validate Input**: Ensure that the `username` and `password` inputs conform to expected formats before passing them to the SQL query. For example, usernames should not contain special characters, and passwords should meet specific complexity requirements.
   ```python
   def validate_input(input_value, pattern):
       """
       Validates the input against a given regex pattern.

       Args:
           input_value (str): The input value to validate.
           pattern (str): The regex pattern to validate against.

       Returns:
           str: The validated input value.

       Raises:
           ValueError: If the input does not match the pattern.
       """
       if not re.match(pattern, input_value):
           raise ValueError("Invalid input format.")
       return input_value
   ```

2. **Integrate Validation**: Update the `login` function to include input validation:
   ```python
   def login():
       while True:
           try:
               username = validate_input(input("Enter username: "), r"^[a-zA-Z0-9_]+$")
               password = validate_input(input("Enter password: "), r"^[a-zA-Z0-9@#$%^&+=]+$")
           except ValueError as e:
               print(e)
               continue
           
           cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", (username, password))
           user = cursor.fetchone()
           
           if user:
               print("Login successful.")
               break
           else:
               print("Incorrect username or password. Please try again.")
   ```

These changes help ensure that only properly formatted inputs are processed, reducing the likelihood of unexpected behavior or vulnerabilities.

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