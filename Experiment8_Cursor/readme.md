# Experiment 8: PL/SQL Cursor Programs

## AIM
To write and execute PL/SQL programs using cursors and exception handling to manage runtime errors effectively and display appropriate messages.

## THEORY

In PL/SQL, cursors are used to handle query result sets row-by-row. 

There are two types of cursors:

- Implicit Cursors: Automatically created by PL/SQL for single-row queries.
- Explicit Cursors: Declared and controlled by the programmer for multi-row queries.

Types of Explicit Cursors:

1. Simple Cursor: Basic cursor to iterate over multiple rows.

2. Parameterized Cursor: Accepts parameters to filter the result dynamically.

3. Cursor FOR Loop: Simplifies cursor operations (open, fetch, close).

4. %ROWTYPE Cursor: Fetches entire row into a record using %ROWTYPE.

5. Cursor with FOR UPDATE: Used for row-level locking and updating the rows while looping.

**Syntax:**
```sql
DECLARE 
   <declarations section> 
BEGIN 
   <executable command(s)>
EXCEPTION 
   <exception handling> 
END;
```

### Basic Components of PL/SQL Block:

- DECLARE: Section to declare variables and constants.
- BEGIN: The execution section that contains PL/SQL statements.
- EXCEPTION: Handles errors or exceptions that occur in the program.
- END: Marks the end of the PL/SQL block.

**Exception Handling**

PL/SQL provides a robust mechanism to handle runtime errors using exception handling blocks. When an error occurs during execution, control is passed to the EXCEPTION section, where specific or general errors can be handled gracefully.

### Components of Exception Handling:
- Predefined Exceptions: Automatically raised by PL/SQL for common errors (e.g., NO_DATA_FOUND, TOO_MANY_ROWS, ZERO_DIVIDE).
- User-defined Exceptions: Declared explicitly in the declaration section using the EXCEPTION keyword.
- WHEN OTHERS: A generic handler for all exceptions not handled explicitly.

```sql
BEGIN
   -- Statements
EXCEPTION
   WHEN exception_name THEN
      -- Handling code
   WHEN OTHERS THEN
      -- Handling for unknown errors
END;
```

### **Question 1: Simple Cursor with Exception Handling**

**Write a PL/SQL program using a simple cursor to fetch employee names and designations from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: When no rows are fetched.
2. **OTHERS**: Any other unexpected errors during execution.

**Steps:**

- Create an `employees` table with fields `emp_id`, `emp_name`, and `designation`.
- Insert some sample data into the table.
- Use a simple cursor to fetch and display employee names and designations.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Program**
```sql
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(50),
    designation VARCHAR2(50)
);


INSERT INTO employees VALUES (1, 'Kamal', 'Manager');
INSERT INTO employees VALUES (2, 'Mani', 'Developer');
INSERT INTO employees VALUES (3, 'Niran', 'Analyst');

COMMIT;

SET SERVEROUTPUT ON;

DECLARE
    CURSOR emp_cursor IS
        SELECT emp_name, designation
        FROM employees;

    v_name employees.emp_name%TYPE;
    v_designation employees.designation%TYPE;

BEGIN
    OPEN emp_cursor;

    FETCH emp_cursor INTO v_name, v_designation;

    IF emp_cursor%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    LOOP
        DBMS_OUTPUT.PUT_LINE(
            'Employee Name: ' || v_name ||
            ' | Designation: ' || v_designation
        );

        FETCH emp_cursor INTO v_name, v_designation;

        EXIT WHEN emp_cursor%NOTFOUND;
    END LOOP;

    CLOSE emp_cursor;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee records found.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
/
```
**Output:**  
<img width="815" height="200" alt="image" src="https://github.com/user-attachments/assets/855f6a1a-8e5d-40a8-b90e-7da605836661" />


---

### **Question 2: Parameterized Cursor with Exception Handling**

**Write a PL/SQL program using a parameterized cursor to retrieve and display employees with a salary in a given range. Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees meet the salary criteria.
2. **OTHERS**: For any unexpected errors during the execution.

**Steps:**

- Modify the `employees` table by adding a `salary` column.
- Insert sample salary values for the employees.
- Use a parameterized cursor to accept a salary range as input and fetch employees within that range.
- Implement exception handling to catch and display relevant error messages.

**Program**
```SQl

ALTER TABLE employees
ADD salary NUMBER;


UPDATE employees
SET salary = 50000
WHERE emp_id = 1;

UPDATE employees
SET salary = 35000
WHERE emp_id = 2;

UPDATE employees
SET salary = 40000
WHERE emp_id = 3;

COMMIT;


SET SERVEROUTPUT ON;

DECLARE
    v_min_salary NUMBER := 30000;
    v_max_salary NUMBER := 45000;

    v_name employees.emp_name%TYPE;
    v_designation employees.designation%TYPE;
    v_salary employees.salary%TYPE;

    CURSOR emp_cursor(p_min NUMBER, p_max NUMBER) IS
        SELECT emp_name, designation, salary
        FROM employees
        WHERE salary BETWEEN p_min AND p_max;

BEGIN
    OPEN emp_cursor(v_min_salary, v_max_salary);

    FETCH emp_cursor INTO v_name, v_designation, v_salary;

    IF emp_cursor%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    LOOP
        DBMS_OUTPUT.PUT_LINE(
            'Employee Name: ' || v_name ||
            ' | Designation: ' || v_designation ||
            ' | Salary: ' || v_salary
        );

        FETCH emp_cursor INTO v_name, v_designation, v_salary;

        EXIT WHEN emp_cursor%NOTFOUND;
    END LOOP;

    CLOSE emp_cursor;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in the given salary range.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
/
```

**Output:**  
<img width="778" height="170" alt="image" src="https://github.com/user-attachments/assets/bb9ea2fa-003c-43e1-a4a7-3e50a2334bc3" />

---

### **Question 3: Cursor FOR Loop with Exception Handling**

**Write a PL/SQL program using a cursor FOR loop to retrieve and display all employee names and their department numbers from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no employees are found in the database.
2. **OTHERS**: For any other unexpected errors.

**Steps:**

- Modify the `employees` table by adding a `dept_no` column.
- Insert sample department numbers for employees.
- Use a cursor FOR loop to fetch and display employee names along with their department numbers.
- Implement exception handling to catch the relevant exceptions.

**Program**
```sql

ALTER TABLE employees
ADD dept_no NUMBER;

UPDATE employees
SET dept_no = 101
WHERE emp_id = 1;

UPDATE employees
SET dept_no = 102
WHERE emp_id = 2;

UPDATE employees
SET dept_no = 103
WHERE emp_id = 3;

COMMIT;


SET SERVEROUTPUT ON;

DECLARE
    v_count NUMBER;

BEGIN
    -- Check whether records exist
    SELECT COUNT(*)
    INTO v_count
    FROM employees;

    IF v_count = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;

    -- Cursor FOR loop
    FOR emp_rec IN (
        SELECT emp_name, dept_no
        FROM employees
    )
    LOOP
        DBMS_OUTPUT.PUT_LINE(
            'Employee Name: ' || emp_rec.emp_name ||
            ' | Department No: ' || emp_rec.dept_no
        );
    END LOOP;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in the database.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
/
```

**Output:**  
<img width="797" height="207" alt="image" src="https://github.com/user-attachments/assets/8185eaeb-07d8-44b7-b3d7-4ec4c1664c85" />


---

### **Question 4: Cursor with `%ROWTYPE` and Exception Handling**

**Write a PL/SQL program that uses a cursor with `%ROWTYPE` to fetch and display complete employee records (emp_id, emp_name, designation, salary). Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees are found in the database.
2. **OTHERS**: For any other errors that occur.

**Steps:**

- Modify the `employees` table by adding `emp_id`, `emp_name`, `designation`, and `salary` fields.
- Insert sample data into the `employees` table.
- Declare a cursor using `%ROWTYPE` to fetch complete rows from the `employees` table.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Program**
```sql
SET SERVEROUTPUT ON;

DECLARE
    -- Cursor declaration
    CURSOR emp_cursor IS
        SELECT emp_id, emp_name, designation, salary, dept_no
        FROM employees;

    -- Record variable using %ROWTYPE
    emp_record emp_cursor%ROWTYPE;

BEGIN
    OPEN emp_cursor;

    FETCH emp_cursor INTO emp_record;

    -- Check if records exist
    IF emp_cursor%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    LOOP
        DBMS_OUTPUT.PUT_LINE(
            'Emp ID: ' || emp_record.emp_id ||
            ' | Name: ' || emp_record.emp_name ||
            ' | Designation: ' || emp_record.designation ||
            ' | Salary: ' || emp_record.salary ||
            ' | Department No: ' || emp_record.dept_no
        );

        FETCH emp_cursor INTO emp_record;

        EXIT WHEN emp_cursor%NOTFOUND;
    END LOOP;

    CLOSE emp_cursor;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in the database.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
/
```
**Output:**  
<img width="803" height="208" alt="image" src="https://github.com/user-attachments/assets/342b8a80-fe2c-46a3-ba5d-e6ca4a658381" />


---

### **Question 5: Cursor with FOR UPDATE Clause and Exception Handling**

**Write a PL/SQL program using a cursor with the `FOR UPDATE` clause to update the salary of employees in a specific department. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no rows are affected by the update.
2. **OTHERS**: For any unexpected errors during execution.

**Steps:**

- Modify the `employees` table to include a `dept_no` and `salary` field.
- Insert sample data into the `employees` table with different department numbers.
- Use a cursor with the `FOR UPDATE` clause to lock the rows of employees in a specific department and update their salary.
- Implement exception handling to handle `NO_DATA_FOUND` or other errors that may occur.

**Program**
```sql
SET SERVEROUTPUT ON;

DECLARE
    -- Department number to update
    v_dept_no NUMBER := 101;

    -- Cursor with FOR UPDATE
    CURSOR emp_cursor IS
        SELECT emp_id, emp_name, salary, dept_no
        FROM employees
        WHERE dept_no = v_dept_no
        FOR UPDATE;

    emp_record emp_cursor%ROWTYPE;

    v_found BOOLEAN := FALSE;

BEGIN
    OPEN emp_cursor;

    LOOP
        FETCH emp_cursor INTO emp_record;

        EXIT WHEN emp_cursor%NOTFOUND;

        v_found := TRUE;

        -- Update salary by adding 5000
        UPDATE employees
        SET salary = salary + 5000
        WHERE CURRENT OF emp_cursor;

        DBMS_OUTPUT.PUT_LINE(
            'Updated Employee: ' || emp_record.emp_name ||
            ' | Department: ' || emp_record.dept_no ||
            ' | New Salary: ' || (emp_record.salary + 5000)
        );
    END LOOP;

    CLOSE emp_cursor;

    -- If no rows found
    IF NOT v_found THEN
        RAISE NO_DATA_FOUND;
    END IF;

    COMMIT;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in the specified department.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
/
```
**Output:**  
<img width="811" height="177" alt="image" src="https://github.com/user-attachments/assets/bf5ac801-3fe0-41b2-9dfa-ffcc27b08622" />


---

## RESULT
Thus, the program successfully executed and displayed employee details using a cursor.
