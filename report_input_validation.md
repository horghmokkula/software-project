# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at pekka.mustonen@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

Inputs for functions like `add_student`, `add_grades`, and `search_student` are not validated. This could lead to invalid or malicious data being stored in the database.

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

### Vulnerable Code

#### Example 1: `add_student` Function
```python
# Vulnerable code
student_number = input("Enter student number: ")
name = input("Enter student name: ")
contact = input("Enter student contact information: ")
ssn = input("Enter student SSN: ")

cursor.execute("INSERT INTO students (student_number, name, contact, ssn) VALUES (?, ?, ?, ?)",
               (student_number, name, contact, ssn))
```

#### Example 2: `add_grades` Function
```python
# Vulnerable code
student_number = input("Enter the student number of the student to add grades for: ")
course = input("Enter course name: ")
grade = input("Enter the grade: ")

cursor.execute("INSERT INTO grades (student_id, course, grade) VALUES (?, ?, ?)",
               (student_number, course, grade))
```

#### Example 3: `search_student` Function
```python
# Vulnerable code
student_number = input("Enter the student number of the student to search for: ")

cursor.execute("SELECT * FROM students WHERE student_number = ?", (student_number,))
student = cursor.fetchone()
```

### Mitigation


## PoC

### Trigger the vulnerability for SQL-injection

During the login, enter the following:
    Username: admin
    Password: ' OR '1'=1
This may bypass authentication

### Trigger the vulnerability for invalid data storage

Add a student with invalid data like:

Enter student number: 123; DROP TABLE students; --
Enter student name: John Doe
Enter student contact information: invalid_contact
Enter student SSN: invalid_ssn

This could corrupt the database or store invalid data

## Impact

Possiblity to gain administrative access to database or to corrupt the database by bypassing the authentication. 

## Remediation

### Suggested function to validate input
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
### Suggested changes to three input-functions using validate_input
#### Updated Code for `add_student`
```python
# Mitigated code
student_number = validate_input(input("Enter student number: "), r"^\d+$")
name = validate_input(input("Enter student name: "), r"^[a-zA-Z\s]+$")
contact = validate_input(input("Enter student contact information: "), r"^\d{10}$")
ssn = encrypt_data(validate_input(input("Enter student SSN: "), r"^\d{9}$"))

cursor.execute("INSERT INTO students (student_number, name, contact, ssn) VALUES (?, ?, ?, ?)",
               (student_number, name, contact, ssn))
```

#### Updated Code for `add_grades`
```python
# Mitigated code
student_number = validate_input(input("Enter the student number of the student to add grades for: "), r"^\d+$")
course = validate_input(input("Enter course name: "), r"^[a-zA-Z\s]+$")
grade = validate_input(input("Enter the grade: "), r"^[A-F]$")

cursor.execute("INSERT INTO grades (student_id, course, grade) VALUES (?, ?, ?)",
               (student_number, course, grade))
```

#### Updated Code for `search_student`
```python
# Mitigated code
student_number = validate_input(input("Enter the student number of the student to search for: "), r"^\d+$")

cursor.execute("SELECT * FROM students WHERE student_number = ?", (student_number,))
student = cursor.fetchone()
```

### Explanation
1. **Input Validation**: Added a `validate_input` function to ensure inputs match expected patterns.
2. **Encryption**: Sensitive data like SSN is encrypted before storage.
3. **Parameterized Queries**: Used parameterized queries to prevent SQL injection.



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
