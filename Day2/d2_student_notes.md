# Day 2 - Dimensional Modelling: Build the Spine

**Purpose in one sentence:** today you learn to design a star schema by declaring its grain first, then build that exact schema in PostgreSQL and prove it with a real query.

---

## 1. Before You Start

**Environment (portable stack, no admin rights needed):**

- PostgreSQL (portable) and DBeaver Community (portable) should be accessible from your USB drive or local folder.
- The OLTP demo database from Day 1 is present and normalised: `students`, `courses`, `instructors`, `enrollments`.
- A warehouse schema exists and is **empty** - the star schema tables do not exist yet. You create them today.
- DBeaver's ERD auto-draw was confirmed working on Day 1.
- Jupyter and Metabase are not used today. ETL begins Day 3; BI begins Day 5.

**Bring with you:** your Day 1 deliverable - the one-page scenario with three business questions the warehouse must answer.

**Carry-forward question, before you start:** look at your three Day 1 questions. For each one, which dimensions does it need (who, what, when), and which measure is it really asking about (a count, a sum, an average)? Keep your answers next to you - you will use them again in Section 3.4 and in Section 3 of the lab.

---

## 2. Theory

### 2.1 Facts vs Dimensions

- **Fact** - a measurement you aggregate: `COUNT`, `SUM`, `AVG`. Facts live in the fact table.
- **Dimension** - descriptive context you slice and group by. Dimensions live in dimension tables.
- **The test:** take the business question "enrollments BY course PER term." What is being counted? `enrollment_count` - that is the fact. What is it grouped by? Course and term - those are dimensions.

In our spine: `enrollment_count` and `grade` live inside `fact_enrollment`. `course_name`, `department`, `semester`, and `nationality` are dimension attributes. `enrollment_count` is an **additive measure** - it sums cleanly across any dimension you group by, which is exactly what makes star-schema queries fast and simple.

**STOP AND CHECK**
1. In the question "average grade BY department," which part is the fact and which is the dimension?
2. Name one dimension attribute that describes a student, and one that describes a course.

### 2.2 Grain

Before you name a single column, answer one question: **what does one row in your fact table represent?** Get this wrong, and every query you write afterwards inherits the mistake.

Grain is the exact business event that one fact row represents. Our spine grain, stated exactly:

> **One row in fact_enrollment represents one student enrolled in one course during one academic term.**

Fine grain keeps every detail; coarse grain is compact but the detail is gone for good.

**Analogy: Camera Resolution**

- *Familiar idea:* choosing the resolution when you take a photo.
- *Mapping:* fine-grain (one row per student per course per term) is like a high-resolution photo - you can zoom into one face in a crowd. Coarse grain (one row per course per term, students summed away) is like a low-resolution photo: smaller, quicker, but that detail was never captured.
- *Where it breaks:* photo resolution is a smooth continuum of pixels; grain is a discrete design decision made up front, not a dial you nudge later.
- *Back to the warehouse:* you can always downscale a high-res photo, but never upscale a low-res one. Likewise, you can roll fine-grained facts up to a summary, but you can never drill a coarse fact back down to detail that was never stored. This is precisely why the rule is to declare the grain first, and as fine as the business process allows.

**STOP AND CHECK**
1. If our fact table stored one row per course per term (students summed away), could you still answer "how many terms has Amara Perera enrolled in Database Systems?" Why or why not?
2. Why is grain declared before any column is named, rather than after?

### 2.3 Star Schema vs Snowflake Schema

**Star schema:** one fact table sits at the centre. Every dimension connects directly to the fact, and only to the fact - dimensions never join to one another in a pure star. Any piece of context is exactly one join away from the numbers you are analysing.

**Analogy: The Solar System**

- *Familiar idea:* the solar system.
- *Mapping:* the Sun at the centre is the fact table; every planet orbits it, just as each dimension connects to the fact. Crucially, planets do not orbit each other - and in a star schema, dimensions likewise connect only to the fact, never to one another.
- *Where it breaks:* the Sun dwarfs the planets by mass, but a fact table is not "more important" - it usually just holds far more rows. An orbit is a continuous gravitational force; a foreign key is a discrete lookup.
- *Back to the warehouse:* the schema is literally called a "star" because of this shape - one fact at the centre, dimensions radiating out, each exactly one join away from the numbers you are analysing.

**Snowflake schema:** a dimension is normalised into further sub-dimensions. Example: `dim_course` splits into `dim_course` + `dim_department`, so department no longer repeats on every course row. The trade: less redundancy, but an extra join to reach it.

| | Star | Snowflake |
|---|---|---|
| Joins per query | Fewer | More |
| Redundancy | More (e.g. department repeats) | Less |
| Ease of querying | Simple, fast | More tables to reason about |
| Maintenance | Fewer tables | More tables to design and maintain |

Try both live in `d2_html_star_schema.html` - the Star / Snowflake toggle shows the join count for "enrollments per department" change from 1 join to 2 as you switch modes.

**STOP AND CHECK**
1. In a pure star schema, can `dim_course` ever have a foreign key pointing at `dim_student`? Why not?
2. Give one realistic reason a team might snowflake a dimension despite the extra join.

### 2.4 Why We Denormalise on Purpose

- OLTP normalises to avoid update anomalies - it is write-heavy.
- The warehouse denormalises because it is read-heavy and write-rare.
- Per Inmon's non-volatile principle from Day 1: once loaded, warehouse data is rarely changed in place.
- Redundancy that would be risky in OLTP is safe, deliberate design in a DW - this is the heart of LO2.

**Analogy: Phone Contacts**

- *Familiar idea:* the contacts app on your phone.
- *Mapping:* each contact card stores the company name in full, directly on the card. If fifty contacts work there, that name is stored fifty times - redundant, but your phone deliberately does this so opening a contact shows the company instantly, with no lookup.
- *Where it breaks:* if the company renames itself, a normalised design updates one row; your phone must update all fifty cards. That is the real cost of denormalisation - reads become simple, updates become expensive.
- *Back to the warehouse:* a star dimension stores its attributes flatly and redundantly, exactly like a contact card - `dim_course` carries `department` on every row rather than pointing to a separate table. Because the warehouse is read-optimised and loaded in batches, that expensive-update cost barely ever bites, so the read simplicity is pure gain.

**STOP AND CHECK**
1. Why is denormalisation "deliberate design" in a warehouse but a mistake in an OLTP system?
2. What would it cost to rename a department if `dim_course` stores `department` flatly, versus if it were snowflaked into `dim_department`?

### 2.5 Worked Example: Designing the Spine

**Step 1 - What are we measuring?** Start from your Day 1 questions - they all name one business process: enrolment. Ask: what event are we measuring, and what triggers one record of it? The event: a student enrols in a course, in a given term. That event is the candidate grain.

**Step 2 - Declare the grain.** Grain: one student, one course, one term - stated before any column exists.
- who -> `dim_student`
- what -> `dim_course`
- when -> `dim_date`

Each "who / what / when" becomes a dimension the fact will reference. Naming the dimensions falls out naturally once the grain is fixed first.

**Step 3 - Assemble the star.** Facts: `enrollment_count` (additive) and `grade` (outcome, filled in later). The three dimension keys sit on the fact row as foreign keys. One fact table, three spokes, one line to each dimension. This exact shape is what you build in SQL below.

**Common modelling mistakes to avoid:**
- Mixing two different grains in one fact table
- Hiding a measure inside a dimension instead of the fact
- Over-snowflaking: normalising dimensions that never needed it
- Forgetting the date dimension entirely
- Choosing a grain too coarse to answer the business question
- Joining one dimension directly to another dimension

---

## 3. Lab - Build It in SQL

### 3.1 Create the Dimensions

Open your SQL editor in DBeaver against the warehouse schema and run each `CREATE TABLE` in order.

```sql
CREATE TABLE dim_student (
    student_key    SERIAL PRIMARY KEY,   -- surrogate key
    student_id     INTEGER NOT NULL,     -- natural key from OLTP
    first_name     VARCHAR(50),
    last_name      VARCHAR(50),
    email          VARCHAR(120),
    date_of_birth  DATE,
    nationality    VARCHAR(50)
);

CREATE TABLE dim_course (
    course_key     SERIAL PRIMARY KEY,   -- surrogate key
    course_id      INTEGER NOT NULL,     -- natural key from OLTP
    course_name    VARCHAR(120),
    department     VARCHAR(80),          -- denormalised onto every row
    credits        INTEGER,
    level          VARCHAR(20)
);

CREATE TABLE dim_date (
    date_key       SERIAL PRIMARY KEY,
    full_date      DATE NOT NULL,
    semester       VARCHAR(20),
    academic_year  VARCHAR(9),    -- e.g. '2023/2024'
    month          INTEGER,
    year           INTEGER,
    quarter        INTEGER
);
```

**Surrogate key vs natural key:** the surrogate key (`student_key`, `course_key`, `date_key`) is warehouse-generated and is what the fact table will reference. The natural key (`student_id`, `course_id`) just traces a row back to its OLTP source - the fact table never uses it directly.

### 3.2 Create the Fact Table

```sql
CREATE TABLE fact_enrollment (
    enrollment_key    SERIAL PRIMARY KEY,
    student_key       INTEGER NOT NULL REFERENCES dim_student(student_key),
    course_key        INTEGER NOT NULL REFERENCES dim_course(course_key),
    date_key          INTEGER NOT NULL REFERENCES dim_date(date_key),
    grade             VARCHAR(2),          -- outcome; NULL until term ends
    enrollment_count  INTEGER NOT NULL DEFAULT 1   -- additive measure
);
```

Every foreign key points at a dimension's surrogate key, never its natural key. This surrogate-FK pattern is what draws the three spokes of the star. `NOT NULL` on every key means a fact row cannot exist without its full context.

**Checkpoint:** each `CREATE TABLE` should return `CREATE TABLE` with no error. If one fails, read the error message (see the Common Errors table below) before retrying.

### 3.3 View the ERD in DBeaver

1. Open the Database Navigator and expand your schema.
2. Right-click the schema node -> **View Diagram** (or open the schema and select the **ER Diagram** tab).
3. DBeaver Community auto-draws the diagram from your foreign keys - no extra setup needed.
4. Confirm the star shape: `fact_enrollment` sits centrally with one line to each of the three dimensions.
5. This diagram, saved or screenshotted, is your ERD deliverable.

### 3.4 Load the Seed Data

Today's data is loaded directly with `INSERT` statements - ETL from the real OLTP source begins Day 3. This is one small, fixed seed dataset, reused identically across this document, the lab slides, and the HTML explorer.

```sql
INSERT INTO dim_student (student_id, first_name, last_name, email, date_of_birth, nationality) VALUES
(1001, 'Amara', 'Perera', 'amara.p@nibm.lk', '2003-04-12', 'Sri Lankan'),
(1002, 'Dilan', 'Fernando', 'dilan.f@nibm.lk', '2002-11-30', 'Sri Lankan'),
(1003, 'Nethmi', 'Silva', 'nethmi.s@nibm.lk', '2003-07-08', 'Sri Lankan'),
(1004, 'Kavindu', 'Jayasuriya', 'kavindu.j@nibm.lk', '2002-02-19', 'Sri Lankan'),
(1005, 'Sanduni', 'Wickramasinghe', 'sanduni.w@nibm.lk', '2003-09-25', 'Sri Lankan'),
(1006, 'Tharindu', 'Bandara', 'tharindu.b@nibm.lk', '2002-12-03', 'Sri Lankan');

INSERT INTO dim_course (course_id, course_name, department, credits, level) VALUES
(2001, 'Database Systems', 'Computing', 15, 'Level 4'),
(2002, 'Web Development', 'Computing', 15, 'Level 4'),
(2003, 'Data Structures', 'Computing', 20, 'Level 4'),
(2004, 'Business Analytics', 'Business', 15, 'Level 4');

INSERT INTO dim_date (full_date, semester, academic_year, month, year, quarter) VALUES
('2023-09-01', 'Semester 1', '2023/2024', 9, 2023, 3),
('2024-02-01', 'Semester 2', '2023/2024', 2, 2024, 1),
('2024-09-01', 'Semester 1', '2024/2025', 9, 2024, 3),
('2025-02-01', 'Semester 2', '2024/2025', 2, 2025, 1);

INSERT INTO fact_enrollment (student_key, course_key, date_key, grade, enrollment_count) VALUES
(1,1,1,'A',1),
(2,2,1,'B',1),
(3,1,1,'B',1),
(4,3,1,'A',1),
(5,2,1,'C',1),
(1,2,2,'B',1),
(2,1,2,'A',1),
(3,3,2,'B',1),
(4,4,2,'A',1),
(5,1,2,'C',1),
(6,2,2,'B',1),
(1,3,3,'A',1),
(2,4,3,'B',1),
(3,2,3,'A',1),
(4,1,3,'B',1),
(5,3,3,'A',1),
(6,4,3,'B',1),
(2,1,3,'A',1),
(1,1,4,NULL,1),
(3,4,4,NULL,1),
(5,2,4,NULL,1),
(6,3,4,NULL,1);
```

Note the seed design: dimension keys are auto-assigned by `SERIAL` in insertion order (so `student_key` 1 = Amara Perera, `course_key` 1 = Database Systems, and so on). Grade is filled in for the three past terms (`date_key` 1-3) and left `NULL` for the current term (`date_key` 4) - the outcome is not yet known at enrolment.

### 3.5 Run the Analytical Query

```sql
SELECT dd.semester, dd.academic_year,
       COUNT(fe.enrollment_key) AS total_enrollments
FROM fact_enrollment fe
JOIN dim_date dd ON fe.date_key = dd.date_key
GROUP BY dd.semester, dd.academic_year
ORDER BY dd.academic_year, dd.semester;
```

**Additive-measure equivalent:** `SUM(fe.enrollment_count)` returns the same totals as `COUNT(fe.enrollment_key)` on this seed. This `SUM` pattern is the one that generalises to every additive measure you meet later in the module.

**Expected output:**

| semester | academic_year | total_enrollments |
|---|---|---|
| Semester 1 | 2023/2024 | 5 |
| Semester 2 | 2023/2024 | 6 |
| Semester 1 | 2024/2025 | 7 |
| Semester 2 | 2024/2025 | 4 |

What correct looks like: a 4-row result, one row per term, with counts that genuinely differ between rows.

### 3.6 Common Errors

| Error or symptom | Cause | Fix |
|---|---|---|
| `relation "dim_student" already exists` | You ran the `CREATE TABLE` twice, or a table from an earlier attempt is still there | `DROP TABLE` the old one first (drop the fact table before its dimensions, since it holds the foreign keys), then re-run |
| `insert or update ... violates foreign key constraint` (FK references missing key) | You inserted a fact row with a `student_key` / `course_key` / `date_key` that does not exist yet | Load all three dimensions completely before loading `fact_enrollment` |
| Column type mismatch on a foreign key | The FK column's data type does not match the referenced primary key's type | Make sure every surrogate key is `SERIAL` (which is `INTEGER` underneath) and every FK column is plain `INTEGER` |
| `column "dd.academic_year" must appear in the GROUP BY clause or be used in an aggregate function` | A selected column was left out of `GROUP BY` | Add every non-aggregated selected column to `GROUP BY` |

### 3.7 Validate Against Day 1 Requirements

Does this three-dimension star answer the questions you sketched on Day 1? Directly answered: enrolment trends by term, by course, and by student demographic (nationality, etc.). Needs more: anything about who taught a course - that needs a fourth dimension we have not built yet. That gap is exactly what the fast-finisher extension below addresses.

**Lab reflection:** how does storing `department` on every course row (instead of in a separate table) change the "enrollments per department" query, compared with a version where department lived in its own table? Try both in `d2_html_star_schema.html` and compare the join count.

### 3.8 Fast Finisher (Optional): dim_instructor

Not part of today's core deliverable. For students who finish early - add a fourth dimension to answer "which lecturers teach the highest-enrolment courses?"

```sql
CREATE TABLE dim_instructor (
    instructor_key   SERIAL PRIMARY KEY,
    instructor_id    INTEGER NOT NULL,
    first_name       VARCHAR(50),
    last_name        VARCHAR(50),
    department       VARCHAR(80)
);

ALTER TABLE fact_enrollment
    ADD COLUMN instructor_key INTEGER REFERENCES dim_instructor(instructor_key);
```

Complete this only after the required schema, ERD, and query are done.

---

## 4. Today's Deliverable

**Working star schema + ERD screenshot + one query result.**

Verification:
- The ERD shows `fact_enrollment` centrally, with one line to each of the three dimensions.
- The query returns a 4-row, one-row-per-term result, with counts that vary between terms.

---

## 5. Key Terms

- **Fact table** - the central table holding measurements (facts) and foreign keys to dimensions.
- **Dimension table** - a table holding descriptive context you slice and group by.
- **Grain** - the exact business event that one fact row represents.
- **Surrogate key** - a warehouse-generated key (e.g. `SERIAL`) that a fact table references; it has no business meaning outside the warehouse.
- **Natural key** - the original business identifier carried over from the source system (e.g. `student_id`).
- **Additive measure** - a fact value that sums correctly across every dimension, such as `enrollment_count`.
- **Star schema** - a design with one fact table connected directly to each dimension, and no dimension-to-dimension joins.
- **Snowflake schema** - a star schema where one or more dimensions are further normalised into sub-dimensions.
- **Denormalisation** - deliberately storing repeated, flattened data to make reads simpler and faster.
- **Foreign key** - a column that references the primary key of another table, here always a dimension's surrogate key.
- **DDL (Data Definition Language)** - the SQL statements, like `CREATE TABLE`, that define a database's structure.

---

## 6. Self-Check

1. *(Recall)* What is the grain statement for `fact_enrollment`, word for word?
2. *(Recall)* Name the three dimensions in the Day 2 spine and the surrogate key each one uses.
3. *(Application)* A colleague proposes storing `department` in its own table and having `dim_course` reference it. What schema type does this create, and what is the trade-off?
4. *(Application)* You need to add "which lecturer taught this enrolment" to the star. Which new table do you add, and where does the new foreign key go?
5. *(Application)* Your analytical query returns only 2 rows instead of 4. List two possible causes and how you would check each one.

---

## 7. Further Reading

- Kimball, R. and Caserta, J. *The Data Warehouse ETL Toolkit* (ISBN 978-0764567575) - dimensional modelling chapters.
- Han, J., Kamber, M. and Pei, J. *Data Mining: Concepts and Techniques* (ISBN 978-0123814791) - Chapter 4, data warehouse and OLAP schemas.
