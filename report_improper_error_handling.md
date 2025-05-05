
# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at pekka.mustonen@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

Errors are handled improperly in many functions like `add_student`, `add_grades`and `search_student`. For example if a database operation fails the program will crash. Crashed program can expose stack traces or other sensitive information for user. 

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

1. **Lack of Exception Handling:**

    Many functions, such as `add_student`, `add_grades`, and `search_student`, do not handle exceptions. For example, if a database operation fails, the program will crash, exposing stack traces or sensitive information.

2. **Exposing Sensitive Information:**

    If an exception occurs, the default Python error messages may reveal sensitive details about the database schema, file paths, or other internal workings of the application.

3. **No User-Friendly Feedback:**

    When errors occur, users are not provided with clear, actionable feedback. Instead, the program may terminate abruptly.

## PoC

### Example of Exploitation
- **Invalid data input**: An attacker could input invalid data (e.g., a string instead of a number) to trigger a database    error. This could be done easily when the program promts for a new student in function `add_student`:
```Python
        student_number = input("Enter student number: ")
        name = input("Enter student name: ")
        contact = input("Enter student contact information: ")
        ssn = input("Enter student SSN: ")
```

## Impact

Improper error handling can lead to several issues, such as exposing sensitive information, crashing the application, or failing to handle unexpected scenarios gracefully.

## Remediation

### Add Exception Handling ###

Use `try-expect` blocks to catch and handle exceptions gracefully. For example:

```Python
    try:
        #Database operation
    except sqlite3.Error as e:
        print("An error occured while accessing the database:",e)
```

### Log Errors Securely ###

Log detailed error messages to a secure log file for debugging purposes, but show generic error messages to users, an example: 

```Python
    import logging

    # Configure logging
    logging.basicConfig(filename='app.log', level=logging.ERROR, format='%(asctime)s - %(levelname)s - %(message)s')

    # Utility function for error handling
    def handle_error(e, user_message):
        logging.error(str(e))
        print(user_message)
```
### Validate Inputs ###

Ensure all inputs are validated before processing to reduce the likehood of errors, one simple way to do this is for example to add `try-expect` block around user input in function `add_student`:

```Python
    try:
        # Get user input
    except sqlite3.Error as e:
        handle_error(e, "An error occurred while adding the student. Please try again.")
    except Exception as e:
        handle_error(e, "An unexpected error occurred. Please contact support.")
```

## GitHub Security Advisories

If possible, please could you create a private [GitHub Security Advisory](https://help.github.com/en/github/managing-security-vulnerabilities/creating-a-security-advisory) for these findings? This allows you to invite me to collaborate and further discuss these findings in private before they are [published](https://help.github.com/en/github/managing-security-vulnerabilities/publishing-a-security-advisory). I will be happy to collaborate with you, and review your fix to make sure that all corner cases are covered. 
When you use a GitHub Security Advisory, you can request a CVE identification number from GitHub. GitHub usually reviews the request within 72 hours, and the CVE details will be published after you make your security advisory public. Publishing a GitHub Security Advisory and a CVE will help notify the downstream consumers of your project, so they can update to the fixed version.

## Credit

Pekka Mustonen and GitHub Copilot

## Contact

pekka.mustonen@student.laurea.fi

## Disclosure Policy

The Best Team on Planet research team is dedicated to working closely with the open source community and with projects that are affected by a vulnerability, in order to protect users and ensure a coordinated disclosure. When we identify a vulnerability in a project, we will report it by contacting the publicly-listed security contact for the project if one exists; otherwise we will attempt to contact the project maintainers directly.

If the project team responds and agrees the issue poses a security risk, we will work with the project security team or maintainers to communicate the vulnerability in detail, and agree on the process for public disclosure. Responsibility for developing and releasing a patch lies firmly with the project team, though we aim to facilitate this by providing detailed information about the vulnerability.

Our disclosure deadline for publicly disclosing a vulnerability is: 90 days after the first report to the project team.

We **appreciate the hard work** maintainers put into fixing vulnerabilities and understand that sometimes more time is required to properly address an issue. We want project maintainers to succeed and because of that we are always open to discuss our disclosure policy to fit your specific requirements, when warranted.
