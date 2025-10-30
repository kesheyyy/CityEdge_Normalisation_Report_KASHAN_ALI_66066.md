```markdown
# CityEdge Sports Academy — Data Normalisation Report

Student: KASHAN ALI  
Student ID: 66066  
Unit: ITDA1001 Database Fundamentals - 2025S3  
Date: 2025-10-30

---

## Introduction

CityEdge Sports Academy stores student enrolment data in a single spreadsheet that mixes student details, multiple sports per student, coaches, results and department information in one flat table. This design causes data duplication, update anomalies and makes reporting difficult. This report applies database normalization (1NF → 2NF → 3NF) to the supplied dataset and produces a final normalized relational schema and example data mapping.

---

## Activity 1 — Normalisation of the Supplied Table

### A. The source data issues

The original flat table (provided) has:
- Composite attributes: Student Name and Contact Address (e.g., "Alex Murphy", "12 Elm St, Greenfield").
- Repeating groups: Sport1/Sport2, Result1/Result2, Coach1/Coach2 and corresponding coach codes.
- Repeated department and coach information across rows, producing redundancy and possible update anomalies.
- Implicit transitive dependencies (for example, CoachCode → CoachName/Department).

### B. First Normal Form (1NF)

Goal: make all fields atomic and remove repeating groups.

Transformations:
- Split `Student Name` into `FirstName` and `LastName`.
- Split `Contact Address` into `StreetAddress` and `Suburb` (plus optional City/Postcode).
- Remove repeating columns (Sport1/Sport2, Result1/Result2, Coach1/Coach2) by creating an `Enrolments` table where each row holds a single Student–Sport pairing.
- Extract distinct entities: `Students`, `Sports`, `Coaches`, `Departments`, `Enrolments`.

1NF logical tables:
- Students(StudentID PK, FirstName, LastName, StreetAddress, Suburb)
- Sports(SportID PK, SportName)
- Coaches(CoachID PK, CoachCode, CoachFullName)
- Departments(DepartmentID PK, DepartmentName)
- Enrolments(EnrollmentID PK or composite (StudentID, SportID), StudentID FK, SportID FK, CoachID FK, Result)

### C. Second Normal Form (2NF)

Goal: remove partial dependencies (applicable when using composite keys).

Design decisions:
- Use composite primary key on Enrolments: (StudentID, SportID) (or include Season and/or use a surrogate EnrollmentID if multiple seasons).
- Keep `Result` and `CoachID` in Enrolments (they depend on Student+Sport).
- Move coach attributes (`CoachCode`, `CoachName`) to `Coaches`.
- Move department information to `Departments` and link `Sports` (and `Coaches`) to Departments.

2NF schema:
- Students(StudentID PK, FirstName, LastName, StreetAddress, Suburb)
- Departments(DepartmentID PK, DepartmentName)
- Sports(SportID PK, SportName, DepartmentID FK)
- Coaches(CoachID PK, CoachCode UNIQUE, FirstName, LastName, DepartmentID FK)
- Enrolments(PK = (StudentID, SportID[, Season]), StudentID FK, SportID FK, CoachID FK, Result)

### D. Third Normal Form (3NF)

Goal: remove transitive dependencies (non-key attributes must not depend on other non-key attributes).

Actions:
- Keep `DepartmentName` only in Departments and reference it from Sports and Coaches using DepartmentID.
- Use CoachID as the primary key in Coaches; CoachCode remains a unique attribute.
- Ensure Students contain no coach or department attributes.

Final 3NF schema (concise):
- Students(StudentID PK, FirstName, LastName, StreetAddress, Suburb, City, Postcode)
- Departments(DepartmentID PK, DepartmentName)
- Sports(SportID PK, SportName, DepartmentID FK)
- Coaches(CoachID PK, CoachCode UNIQUE, FirstName, LastName, DepartmentID FK)
- Enrolments(StudentID FK, SportID FK, CoachID FK, Result, Season, PRIMARY KEY(StudentID, SportID, Season))

Notes:
- If the same student may enroll in the same sport each season, include `Season` in the primary key or use an `EnrollmentID` surrogate and a unique constraint on (StudentID, SportID, Season).
- This structure removes redundancy and avoids update anomalies.

---

## Activity 2 — Apply 3NF to a Typical First-Assignment Design

Typical initial (unnormalized) design:
- A single table `AcademyEnrollments` containing `StudentName`, `ContactAddress`, `Sport1`, `Result1`, `Coach1`, `CoachCode1`, `Sport2`, `Result2`, `Coach2`, `CoachCode2`, `Department`.

Issues:
- Multi-valued sport fields (violates 1NF).
- Coach and department fields repeated per row causing redundancy.
- Transitive dependencies where department details are derivable from sport or coach.

3NF application:
- Convert multi-valued fields into rows in Enrolments (1NF).
- Extract Students, Coaches and Departments (2NF).
- Remove transitive dependencies by referencing DepartmentID from Sports and Coaches (3NF).

Result:
- The normalized schema is the same as derived in Activity 1 (Students, Departments, Sports, Coaches, Enrolments).

---

## Conclusion

- Converting the flat sheet into a normalized 3NF schema eliminates repeating groups, partial and transitive dependencies.
- The final schema improves maintainability, removes update anomalies and simplifies queries like "list students in a sport" or "retrieve all students coached by X".
- Next possible steps: create the physical database with the provided SQL DDL, insert sample data, and produce an ER diagram.

---

## Appendix: Sample Tables (Normalized rows mapped from the supplied data)

### Students
StudentID | FirstName | LastName | StreetAddress | Suburb
--- | --- | --- | --- | ---
1 | Alex | Murphy | 12 Elm St | Greenfield
2 | Lina | Patel | 34 Oak Dr | Lakeside
3 | Ethan | Lee | 7 Rose Ln | Rivertown
4 | Sarah | White | 90 Pine Rd | Sunnyside
5 | Noah | Kim | 101 Maple Ave | Hilltop

### Departments
DepartmentID | DepartmentName
--- | ---
1 | Athletics
2 | Racquet
3 | Precision
4 | Acrobatics

### Sports
SportID | SportName | DepartmentID
--- | --- | ---
1 | Basketball | 1
2 | Swimming | 1
3 | Tennis | 2
4 | Badminton | 2
5 | Archery | 3
6 | Gymnastics | 4
7 | Football | 1

### Coaches
CoachID | CoachCode | FirstName | LastName | DepartmentID
--- | --- | --- | --- | ---
1 | C001 | Mr. | Carson | 1
2 | C002 | Ms. | Vega | 1
3 | C003 | Ms. | Wong | 2
4 | C004 | Mr. | Thomas | 2
5 | C005 | Mr. | Nair | 3
6 | C006 | Ms. | Green | 4
7 | C007 | Mr. | O'Neil | 1

### Enrolments
StudentID | SportID | CoachID | Result | Season
--- | --- | --- | --- | ---
1 | 1 | 1 | Pass | 2025-S1
1 | 2 | 2 | Merit | 2025-S1
2 | 3 | 3 | Merit | 2025-S1
2 | 4 | 4 | Pass | 2025-S1
3 | 5 | 5 | Dist | 2025-S1
4 | 6 | 6 | Pass | 2025-S1
5 | 7 | 7 | Merit | 2025-S1

---

## Appendix: Example SQL DDL (3NF)

```sql
-- Departments
CREATE TABLE Departments (
  DepartmentID INT PRIMARY KEY,
  DepartmentName VARCHAR(100) NOT NULL
);

-- Students
CREATE TABLE Students (
  StudentID INT PRIMARY KEY,
  FirstName VARCHAR(50) NOT NULL,
  LastName VARCHAR(50) NOT NULL,
  StreetAddress VARCHAR(150),
  Suburb VARCHAR(100),
  City VARCHAR(100),
  Postcode VARCHAR(20)
);

-- Sports
CREATE TABLE Sports (
  SportID INT PRIMARY KEY,
  SportName VARCHAR(100) NOT NULL,
  DepartmentID INT,
  FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);

-- Coaches
CREATE TABLE Coaches (
  CoachID INT PRIMARY KEY,
  CoachCode VARCHAR(20) UNIQUE,
  FirstName VARCHAR(50),
  LastName VARCHAR(50),
  DepartmentID INT,
  FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);

-- Enrolments
CREATE TABLE Enrolments (
  StudentID INT,
  SportID INT,
  CoachID INT,
  Result VARCHAR(20),
  Season VARCHAR(20),
  PRIMARY KEY (StudentID, SportID, Season),
  FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
  FOREIGN KEY (SportID) REFERENCES Sports(SportID),
  FOREIGN KEY (CoachID) REFERENCES Coaches(CoachID)
);
```

```
End of report
```
