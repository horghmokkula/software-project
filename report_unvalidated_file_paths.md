
# Vulnerability Report

I identified potential security vulnerabilities in Software Project.

I am committed to working with you to help resolve these issues. In this report you will find everything you need to effectively coordinate a resolution of these issues.

If at any point you have concerns or questions about this process, please do not hesitate to reach out to me at pekka.mustonen@student.laurea.fi.

If you are _NOT_ the correct point of contact for this report, please let me know!

## Summary

There is an issue with unvalidated file paths in the functions `upload_image` and `download_student_image` allowing users to specify file paths. This could lead to directory traversal attacks if malicious paths are provided.

## Product

Software Project

## Tested Version

1.0? (Version information unavailabe)

## Details

### What is Unvalidated File Path?
Unvalidated file path is a security vulnerability that allows attackers to access files and directories outside the intended directory. This is achieved by manipulating file paths, often using sequences like `../` to traverse up the directory tree.

### How it Affects the Application
In the context of `upload_image` and `download_student_image` functions:
- **Upload Image**: If the file path is not sanitized, an attacker could upload files to unauthorized locations, potentially overwriting critical files.
- **Download Image**: If the file path is not validated, an attacker could access sensitive files outside the intended directory.

## PoC

### Example of Exploitation
- **Upload**: An attacker provides a file path like `../../etc/passwd` to overwrite the system's password file.
- **Download**: An attacker requests a file path like `../../etc/shadow` to access sensitive system files.

## Impact

An attacker can gain unauthorized access to sensitive files. This also may allow overwriting critical system files making the system unstable or vulnerable to security breaches.  

## Remediation

1. **Sanitize File Paths**:
   - Use libraries like `os.path` to normalize and validate file paths.
   - Ensure the file path is within the intended directory.

   Example:
   ```python
   import os

   def sanitize_path(file_path, base_directory):
       # Normalize the path
       normalized_path = os.path.normpath(os.path.join(base_directory, file_path))
       # Ensure the path is within the base directory
       if not normalized_path.startswith(base_directory):
           raise ValueError("Invalid file path")
       return normalized_path
   ```

2. **Restrict File Types**:
   - Validate the file type and extension before processing.
   - Example: Only allow `.png` or `.jpg` files for image uploads.

3. **Use a Whitelist**:
   - Maintain a whitelist of allowed file names or paths.

4. **Error Handling**:
   - Implement proper error handling to avoid exposing sensitive information in error messages.

5. **Run the Application with Limited Privileges**:
   - Ensure the application runs with the least privileges necessary to minimize the impact of a successful attack.

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
