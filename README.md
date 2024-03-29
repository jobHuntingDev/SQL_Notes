# SQL Notes

## Tables

### Composite key

This is when you define two values as your primary key. This is often done when one column alone is not enough to identify entities.

### Foreign key

A value we can store in out table that links us to values from another table.Usually the value of the foreign key is the primary key of the linked row(from the other table). 

**Employee**

| emp id | first name | salary | branch id |
| 100    | Jan        | 75,000 | 1         |
| 101    | Michael    | 78,000 | 2         |

**Branch**
| branch id| branch name| mgr id|
| 2        | Scranton   | 101   |
| 3        | Stamford   | 102   |
| 1        | Coporate   | 108   |

The foreign key between Employee and Branch table here is "branch id"

Foreign keys can also be used to define relationships between values in a single table.

For example:

| emp id | first name | salary | supervisor id |
| 100    | Jan        | 75,000 | NULL      	   |
| 101    | Michael    | 78,000 | NULL          |
| 102    | Josh       | 65,000 | 101           |
| 103    | Andy       | 63,000 | 100           |

Andy's supervisor is Jan.

![Table example](Example1.png)

**Note: Primary Key(Red),Foreign Key(Green)**


## The SQL Language


### Tables

Common datatypes

- INT : Whole Number
- DECIMAL(M,N): Decimal numbers, M: Total number of digits you want to store, N: How many of said numbers(M) are after the decimal point.
- VARCHAR(1): String of text of length 1
- BLOB: Binary Large Object, Stores large data, often used to store images or files
- DATE: 'YYYY-MM-DD'
- TIMESTAMP: 'YYYY-MM-DD HH:MM:SS' used for recording when entry was saved.

#### Creating a table

| student_id | name       | major     | 
| 1          | Jack       | Biology   | 
| 2          | Kate       | Sociology | 
| 3          | Claire     | English   | 
| 4          | Jack       | Biology   | 
| 5          | Mike       | Comp.Sci  | 

```sql
CREATE TABLE students (
	student_id INT PRIMARY KEY,
	name VARCHAR(20),
	major VARCHAR(20)
);
```

OR

A different way to assign the primary key column

```sql
CREATE TABLE students (
	student_id INT,
	name VARCHAR(20),
	major VARCHAR(20),
	PRIMARY KEY(student_id)
);
```

A table with constraints on columns.Specifically not null and unique.

UNIQUE:Values in this column have to be unique.
NOT NULL: This value can not be blank.
AUTO_INCREMENT: Automatically increment.

```sql
CREATE TABLE student(
	student_id INT AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL,
	major VARCHAR(20) UNIQUE,
	PRIMARY KEY(student_id)
);
```

Creating table with a default value

```sql
CREATE TABLE student(
	student_id INT,
	name VARCHAR(20) NOT NULL,
	major VARCHAR(20) DEFAULT 'undecided',
	PRIMARY KEY(student_id)
);
```

#### Delete a table

```sql
DROP TABLE students;
```

#### Modify a table

Add a gpa column.

```sql
ALTER TABLE students ADD gpa DECIMAL(3, 2);
```

Delete gpa column.

```sql
ALTER TABLE students DROP COLUMN gpa;
```

### Inserting Data

```sql
INSERT INTO students VALUES (1,'Jack','Biology);
```

Specifiying columns

```sql
INSERT INTO students(student_id, name) VALUES (2, 'Kate');
```

### Update & Delete

Changing the major standard(how it's witten) from "Biology" to "Bio" in the students table.

```sql
UPDATE student
SET major = "Bio"
WHERE major = "Biology";
```

A more complex data update.

```sql
UPDATE student
SET major = "Bio-Chemistry"
WHERE major = "Bio"
OR major = "Chemistry";
```

Update multiple values.

```sql
UPDATE student
SET name = "Tom", major= "undecided"
WHERE student_id = 1;
```

Update every row.

```sql
UPDATE student
SET major = "undecided";
```

Delete all rows in student table

```sql
DELETE FROM student;
```

Delete specific rows

```sql
DELETE FROM student
WHERE student_id = 5;
```

Row selection (WHERE) works the same as the previous Update examples

### Basic Queries

Getting all the data in the student table.

```sql
SELECT * 
FROM student;
```

Get all the values of the name column

```sql
SELECT name,major
FROM student;
```
OR

```sql
SELECT student.name, student.major
FROM student;
```

Get name and major in aphabetical order(by name).

```sql
SELECT student.name, student.major
FROM student;
ORDER BY name;
```

Get names and major in decending alphabetical order(by major).

```sql
SELECT student.name, student.major
FROM student;
ORDER BY major DESC;
```

Order everyting by major but if two match order them by student_id.

```sql
SELECT *
FROM student;
ORDER BY major,student_id;
```

Limit the result down to two.

```sql
SELECT *
FROM student;
LIMIT 2;
```

Filter out students: Get only Biology majors.

```sql
SELECT student_id, name, major
FROM student;
WHERE major = "Biology";
```

Get all the students with the name: Claire or Kate or Mike

```sql
SELECT *
FROM student
WHERE name IN ('Claire','Kate','Mike');
```

More complex filtering

```sql
SELECT *
FROM student
WHERE name IN ('Claire','Kate','Mike') AND student_id > 2;
```

### Company Database

A more complex and realistic database.

Primary_key: Red
Foreign_key: Green

![Company database](company-database.pdf)


### Create Company Database

Create employee table.

```sql
CREATE TABLE employee (
  emp_id INT PRIMARY KEY,
  first_name VARCHAR(40),
  last_name VARCHAR(40),
  birth_day DATE,
  sex VARCHAR(1),
  salary INT,
  super_id INT,
  branch_id INT
);
```

Create branch table.

```sql
CREATE TABLE branch (
  branch_id INT PRIMARY KEY,
  branch_name VARCHAR(40),
  mgr_id INT,
  mgr_start_date DATE,
  FOREIGN KEY(mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);
```

Set foreign keys.

```sql
ALTER TABLE employee
ADD FOREIGN KEY(branch_id)
REFERENCES branch(branch_id)
ON DELETE SET NULL;

ALTER TABLE employee
ADD FOREIGN KEY(super_id)
REFERENCES employee(emp_id)
ON DELETE SET NULL;
```

Create client table.

With Foreign key relationship.

```sql
CREATE TABLE client (
  client_id INT PRIMARY KEY,
  client_name VARCHAR(40),
  branch_id INT,
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL
);
```

Create works_with table.

```sql
CREATE TABLE works_with (
  emp_id INT,
  client_id INT,
  total_sales INT,
  PRIMARY KEY(emp_id, client_id),
  FOREIGN KEY(emp_id) REFERENCES employee(emp_id) ON DELETE CASCADE,
  FOREIGN KEY(client_id) REFERENCES client(client_id) ON DELETE CASCADE
);
```

Create branch_supplier table.

```sql
CREATE TABLE branch_supplier (
  branch_id INT,
  supplier_name VARCHAR(40),
  supply_type VARCHAR(40),
  PRIMARY KEY(branch_id, supplier_name),
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);
```

Insert Data

```sql
-- Corporate
INSERT INTO employee VALUES(100, 'David', 'Wallace', '1967-11-17', 'M', 250000, NULL, NULL);

INSERT INTO branch VALUES(1, 'Corporate', 100, '2006-02-09');

UPDATE employee
SET branch_id = 1
WHERE emp_id = 100;

INSERT INTO employee VALUES(101, 'Jan', 'Levinson', '1961-05-11', 'F', 110000, 100, 1);

-- Scranton
INSERT INTO employee VALUES(102, 'Michael', 'Scott', '1964-03-15', 'M', 75000, 100, NULL);

INSERT INTO branch VALUES(2, 'Scranton', 102, '1992-04-06');

UPDATE employee
SET branch_id = 2
WHERE emp_id = 102;

INSERT INTO employee VALUES(103, 'Angela', 'Martin', '1971-06-25', 'F', 63000, 102, 2);
INSERT INTO employee VALUES(104, 'Kelly', 'Kapoor', '1980-02-05', 'F', 55000, 102, 2);
INSERT INTO employee VALUES(105, 'Stanley', 'Hudson', '1958-02-19', 'M', 69000, 102, 2);

-- Stamford
INSERT INTO employee VALUES(106, 'Josh', 'Porter', '1969-09-05', 'M', 78000, 100, NULL);

INSERT INTO branch VALUES(3, 'Stamford', 106, '1998-02-13');

UPDATE employee
SET branch_id = 3
WHERE emp_id = 106;

INSERT INTO employee VALUES(107, 'Andy', 'Bernard', '1973-07-22', 'M', 65000, 106, 3);
INSERT INTO employee VALUES(108, 'Jim', 'Halpert', '1978-10-01', 'M', 71000, 106, 3);


-- BRANCH SUPPLIER
INSERT INTO branch_supplier VALUES(2, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Patriot Paper', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'J.T. Forms & Labels', 'Custom Forms');
INSERT INTO branch_supplier VALUES(3, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(3, 'Stamford Lables', 'Custom Forms');

-- CLIENT
INSERT INTO client VALUES(400, 'Dunmore Highschool', 2);
INSERT INTO client VALUES(401, 'Lackawana Country', 2);
INSERT INTO client VALUES(402, 'FedEx', 3);
INSERT INTO client VALUES(403, 'John Daly Law, LLC', 3);
INSERT INTO client VALUES(404, 'Scranton Whitepages', 2);
INSERT INTO client VALUES(405, 'Times Newspaper', 3);
INSERT INTO client VALUES(406, 'FedEx', 2);

-- WORKS_WITH
INSERT INTO works_with VALUES(105, 400, 55000);
INSERT INTO works_with VALUES(102, 401, 267000);
INSERT INTO works_with VALUES(108, 402, 22500);
INSERT INTO works_with VALUES(107, 403, 5000);
INSERT INTO works_with VALUES(108, 403, 12000);
INSERT INTO works_with VALUES(105, 404, 33000);
INSERT INTO works_with VALUES(107, 405, 26000);
INSERT INTO works_with VALUES(102, 406, 15000);
INSERT INTO works_with VALUES(105, 406, 130000);
```
### More Basic Queries
Continue 2:30:27 
### Functions
### Wildcards
### Union
### Joins
### Nested Queries
### On Delete
### Triggers
### ER Diagrams Intro
### Designing an ER Diagraam
## Converting ER Diagrams to Schemas
