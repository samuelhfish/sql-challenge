# sql-challenge
DataViz SQL Challenge

Code listed below and also in the EmployeeSQL folder. The file is titled "PewlettHackard.sql"

ERD is located in the EmployeeSQL folder and the file is titled "ERD-SQL-Challenge.png"

-- Exported table structure from QuickDBD: https://www.quickdatabasediagrams.com/


    CREATE TABLE employees (
        employee_number INT   NOT NULL,
        employee_title_id VARCHAR(30)   NOT NULL,
        birth_date DATE   NOT NULL,
        first_name VARCHAR(30)   NOT NULL,
        last_name VARCHAR(30)   NOT NULL,
        sex VARCHAR(30)   NOT NULL,
        hire_date DATE   NOT NULL,
        CONSTRAINT pk_employees PRIMARY KEY (
        employee_number
        )
    );

    CREATE TABLE salaries (
        employee_number INT   NOT NULL,
        salary FLOAT   NOT NULL,
        CONSTRAINT pk_salaries PRIMARY KEY (
        employee_number
        )
    );

    CREATE TABLE department_employees (
        employee_number INT   NOT NULL,
        department_number VARCHAR(30)   NOT NULL,
        FOREIGN KEY (employee_number) REFERENCES employees (employee_number),
        FOREIGN KEY (department_number) REFERENCES departments (department_number),
        PRIMARY KEY (employee_number, department_number)
    );

    CREATE TABLE deptartment_managers (
        department_number VARCHAR(30)   NOT NULL,
        employee_number INT   NOT NULL,
        FOREIGN KEY (employee_number) REFERENCES employees (employee_number),
        FOREIGN KEY (department_number) REFERENCES departments (department_number),
        PRIMARY KEY (department_number, employee_number)
    );

    CREATE TABLE departments (
        department_number VARCHAR(30)   NOT NULL,
        department_name VARCHAR(30)   NOT NULL,
        CONSTRAINT pk_departments PRIMARY KEY (
        department_number
        )
    );

    CREATE TABLE titles (
        employee_title_id VARCHAR(30)   NOT NULL,
        title VARCHAR(30)   NOT NULL,
        CONSTRAINT pk_titles PRIMARY KEY (
        employee_title_id
        )
    );

    ALTER TABLE salaries ADD CONSTRAINT fk_employees_employee_number FOREIGN KEY(employee_number)
    REFERENCES employees (employee_number);

    ALTER TABLE employees ADD CONSTRAINT fk_employees_employee_title_id FOREIGN KEY(employee_title_id)
    REFERENCES titles (employee_title_id);

    ALTER TABLE department_employees ADD CONSTRAINT fk_department_employees_employee_number FOREIGN KEY(employee_number)
    REFERENCES employees (employee_number);

    ALTER TABLE department_employees ADD CONSTRAINT fk_department_employees_department_number FOREIGN KEY(department_number)
    REFERENCES departments (department_number);

    ALTER TABLE department_managers ADD CONSTRAINT fk_department_managers_department_number FOREIGN KEY(department_number)
    REFERENCES departments (department_number);

    ALTER TABLE department_managers ADD CONSTRAINT "fk_department_managers_employee_number" FOREIGN KEY("employee_number")
    REFERENCES employees (employee_number);

    SELECT * FROM employees
    SELECT * FROM titles
    SELECT * FROM salaries
    SELECT * FROM departments
    SELECT * FROM department_employees
    SELECT * FROM department_managers

-- 1. List the employee number, last name, first name, sex, and salary of each employee.
    
    SELECT employees.employee_number,
        employees.last_name,
        employees.first_name,
        employees.sex,
        salaries.salary
    FROM employees
    INNER JOIN salaries ON
    salaries.employee_number=employees.employee_number;

-- 2. List the first name, last name, and hire date for the employees who were hired in 1986.
    
    SELECT employees.employee_number,
        employees.last_name,
        employees.first_name,
        employees.hire_date
    FROM employees
    WHERE hire_date BETWEEN '1986-1-1' AND '1986-12-31'
    ORDER BY hire_date ASC;

-- 3. List the manager of each department along with their department number, department name, employee number, last name, and first name.
    
    SELECT
                department_managers.department_number,
                departments.department_name,
                department_managers.employee_number,
                employees.last_name,
                employees.first_name

    FROM
                department_managers
                INNER JOIN employees ON employees.employee_number = department_managers.employee_number
                INNER JOIN departments ON departments.department_number = department_managers.department_number
                
    ORDER BY department_number ASC;

-- 4. List the department number for each employee along with that employee’s employee number, last name, first name, and department name.
              
    SELECT
                department_employees.department_number,
                department_employees.employee_number,
                employees.last_name,
                employees.first_name,
                departments.department_name

    FROM
                department_employees
                INNER JOIN employees ON employees.employee_number = department_employees.employee_number
                INNER JOIN departments ON departments.department_number = department_employees.department_number
                
    ORDER BY department_number ASC;

-- 5. List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B.

    SELECT employees.first_name,
        employees.last_name,
        employees.sex
    FROM employees
    WHERE first_name = 'Hercules' AND last_name LIKE 'B%'
    ORDER BY last_name ASC;

-- 6. List each employee in the Sales department(d007), including their employee number, last name, and first name.

    SELECT employees.employee_number,
        employees.last_name,
        employees.first_name
    FROM employees
    INNER JOIN department_employees ON
    department_employees.employee_number=employees.employee_number
    WHERE department_number = 'd007'
    ORDER BY last_name ASC;

-- 7. List each employee in the Sales(d007) and Development(d005) departments, including their employee number, last name, first name, and department name.

    SELECT employees.employee_number,
        employees.last_name,
        employees.first_name
    FROM employees
    INNER JOIN department_employees ON
    department_employees.employee_number=employees.employee_number
    WHERE department_number = 'd007' OR department_number = 'd005'
    ORDER BY last_name ASC;

-- 8. List the frequency counts, in descending order, of all the employee last names (that is, how many employees share each last name).

    SELECT last_name, COUNT(last_name) AS "last name count"
    FROM employees
    GROUP BY last_name
    ORDER BY "last name count" DESC;