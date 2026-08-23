# Glossary

Every word this module introduces, in one place. The **Day** column shows the day the word was first taught. A word keeps that day even when a later day uses it again.

Use this list when a word in the slides or the notes is unfamiliar. The words are in alphabetical order.

---

| Term | Day | What it means | Example |
|---|---|---|---|
| **5Vs** | 1 | The five properties used to describe big data: volume, velocity, variety, veracity and value. | Social media posts arrive fast and in many formats, so they score high on velocity and variety. |
| **Additive measure** | 2 | A fact value that sums correctly across every dimension. | `enrollment_count` can be summed by course, by term, or by both. |
| **Big data** | 1 | Datasets too large or too complex for traditional tools to handle. | Every click on a national e-commerce site for one year. |
| **DAMA UK** | 1 | The Data Management Association, which publishes the six data quality dimensions. | The six dimensions are used to score a raw file before it is cleaned. |
| **Data** | 1 | Raw facts and figures, with no context added yet. | `student_id: S001, course_id: CS101, grade: 72` |
| **Data warehouse** | 1 | A subject-oriented, integrated, time-variant and non-volatile store of data, built for analysis. | The enrollment warehouse this module builds across the week. |
| **DBeaver** | 1 | The open-source SQL client and diagram viewer used in this module. | You write `CREATE TABLE` in DBeaver and view the ERD it draws. |
| **DDL** | 2 | Data Definition Language. The SQL statements that define the structure of a database. | `CREATE TABLE dim_course (...)` is a DDL statement. |
| **Denormalisation** | 1 | Deliberately storing repeated, flattened data so reads are simpler and faster. | Storing `department_name` inside `dim_course` instead of in a separate table. |
| **Dimension table** | 1 | A table holding descriptive context that you slice and group by. | `dim_course` holds the course code, title and department. |
| **DSS** | 1 | Decision Support System. A computer system that helps a person make a decision. | A report that shows whether to open another intake. |
| **ETL** | 1 | Extract, Transform, Load. The process that moves data from a source system into a warehouse. | Reading a raw CSV, cleaning it, then loading it into the warehouse. |
| **Fact table** | 1 | The central table holding the measurements, plus foreign keys to each dimension. | `fact_enrollment` holds one row per enrollment. |
| **Foreign key** | 2 | A column that references the primary key of another table. | `fact_enrollment.course_key` references `dim_course.course_key`. |
| **Grain** | 1 | The exact business event that one row of the fact table represents. | One row represents one student enrolled in one course during one academic term. |
| **Information** | 1 | Data that has been given context and meaning. | "72 is the grade CS101 gave to student S001 in Semester 1." |
| **Inmon** | 1 | Bill Inmon, who defined the data warehouse in 1990. | Inmon's four properties are used to test whether a store is a warehouse. |
| **Kimball** | 1 | Ralph Kimball, who developed the dimensional modelling approach in 1996. | The star schema in this module follows Kimball's approach. |
| **Knowledge** | 1 | Understanding gained from information, which lets you act. | "Grades fall in courses with more than 60 students, so cap the intake." |
| **Metadata** | 1 | Data about data. It describes the context, origin and format of something. | The column names, data types and last-updated date of a table. |
| **Natural key** | 2 | The original identifier carried over from the source system. | `student_id` as the registry writes it, such as `S001`. |
| **Normalisation** | 1 | A design principle that stores each fact exactly once, in one place. | Splitting `courses` and `departments` into two tables so a department name is stored once. |
| **OLAP** | 1 | Online Analytical Processing. Reading large amounts of history to answer a question. | Counting enrollments per department across four terms. |
| **OLTP** | 1 | Online Transaction Processing. Recording operational events as they happen. | Saving one new enrollment as a student registers. |
| **PostgreSQL** | 1 | The open-source database engine used for both the source system and the warehouse. | Both the OLTP demo database and the warehouse run on PostgreSQL. |
| **Semi-structured data** | 1 | Data with partial or inconsistent structure. | A JSON file where some records carry fields that others do not. |
| **Snowflake schema** | 2 | A star schema where one or more dimensions are split further into sub-dimensions. | `dim_course` splitting out into a separate `dim_department` table. |
| **Star schema** | 1 | A design with one fact table joined directly to each dimension, and no dimension-to-dimension joins. | `fact_enrollment` joined to `dim_student`, `dim_course` and `dim_date`. |
| **Structured data** | 1 | Data held in a consistent, organised format. | Rows and columns in a SQL table. |
| **Surrogate key** | 1 | A key the warehouse generates itself. It has no meaning in the source system. | `student_key` counting 1, 2, 3, while the source still uses `S001`. |
| **Unstructured data** | 1 | Data with no predefined format. | Photographs, audio recordings and free text. |

---

## How this list grows

A new set of words is added on the day it is taught. Nothing is ever removed, and no word changes its day tag.

Words currently listed: 31, covering Days 1 and 2.

---

*Version 1.0 · 23 August 2026*
