The following changes were made to main_secure.py to address security vulnerabilities:

1. **Hardcoded Credentials**:
   - Removed hardcoded credentials and replaced them with a secure default admin user using hashed passwords (`bcrypt`).

2. **SQL Injection**:
   - Replaced all string-interpolated SQL queries with parameterized queries to prevent SQL injection.

3. **Sensitive Data Storage**:
   - Passwords are now hashed using `bcrypt` before storing them in the database.

4. **Improper Input Validation**:
   - Added input validation using regular expressions to ensure inputs like `student_number` and `SSN` are sanitized.

5. **Directory Traversal**:
   - Restricted file paths to a specific directory (`images`) and validated file names using `os.path.basename`.

6. **Resource Leak**:
   - Ensured the database connection is properly closed at the end of the program.

7. **Insecure Image Handling**:
   - Validated file paths and ensured images are securely uploaded and downloaded.