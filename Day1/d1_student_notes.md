# Day 1 Student Notes
## Data Warehousing and Business Intelligence
### NIBM / Coventry University · BSc (Hons) Computing & HND Software Engineering · Level 4

---

## Learning Objectives for Today

By the end of Day 1 you should be able to:

- Define data, information, and knowledge, and explain the relationship between them
- Identify structured, unstructured, and semi-structured data with examples
- Describe the 5Vs of big data and the six DAMA UK data quality dimensions
- Explain what a Decision Support System (DSS) is and how it evolved since the 1960s
- Distinguish between OLTP and OLAP and explain when each is used
- State Inmon's four defining properties of a data warehouse
- Explain why a data warehouse uses a denormalised star schema
- Identify the OLTP and DW schemas in DBeaver

---

## Part 1 - Data Concepts (Session 6.1.A)

### 1.1 What is Data?

**Data** is raw facts and figures - unprocessed, uncontextualised, and uninterpreted.

Examples:
- `student_id: S001, course_id: CS101, grade: 72` - NIBM enrollment record
- `temperature: 28, humidity: 85, time: 09:00` - Colombo weather reading
- `item: rice, quantity: 2, price: 450, timestamp: 2024-01-15 10:32` - Cargills Food City POS

The number `47` alone is data. It tells you nothing. 47 what? From where? Compared to what? **Context is what turns raw data into something useful.**

---

### 1.2 Quantitative vs Qualitative Data

| Type | Definition | Examples |
|------|-----------|---------|
| **Quantitative** | Numerical, measurable | Height, weight, count, temperature |
| **Qualitative** | Descriptive, non-numerical | Feedback, opinions, descriptions, categories |

**NIBM context:**
- Number of students who passed the exam = **quantitative**
- A lecturer's written feedback on your essay = **qualitative**

**Why this matters in DWBI:** Quantitative data drives the *measures* in your fact table (enrollment count, grade average). Qualitative data describes your *dimension attributes* (course category, student nationality, department name). The distinction is architectural.

---

### 1.3 Data → Information → Knowledge

These three levels form the **DIK hierarchy**:

| Level | Definition | Example |
|-------|-----------|---------|
| **Data** | Raw, unprocessed facts | The number `47` |
| **Information** | Data with context and meaning | `47 students passed Semester 1 Computing` |
| **Knowledge** | Understanding gained from information; ability to act | The registrar opens a new intake based on that figure |

#### Analogy: The Kitchen

| Part | Maps to |
|------|--------|
| Raw ingredients (flour, eggs, sugar) | **Data** - alone, meaningless |
| A recipe | **Information** - quantities, sequence, method give context |
| A skilled cook who adapts without measuring | **Knowledge** - internalised information, applied with judgment |

**Where it breaks:** A recipe is static. Information in a warehouse is queried on demand and changes as new data is loaded.

**DW reconnect:** Raw transaction records = data. A semester enrollment report = information. The registrar opening a new intake based on that report = knowledge applied. The data warehouse is the kitchen that makes this possible at scale.

---

### 1.4 Types of Data

| Type | Definition | Example |
|------|-----------|---------|
| **Structured** | Organised, consistently formatted, easily queried | SQL tables, spreadsheets |
| **Unstructured** | No predefined format | Images, videos, social media posts |
| **Semi-structured** | Some organisation, but applied inconsistently | JSON files, email, PDF invoices |

**Cargills Food City example:**
- POS sales records = **structured** (item, quantity, price, timestamp - same shape every row)
- Customer complaints via WhatsApp = **unstructured**
- Supplier invoices emailed as PDF attachments = **semi-structured**

#### Analogy: The Kitchen Cupboard

| Part | Maps to |
|------|--------|
| Labelled Tupperware with measured portions | **Structured** - you know exactly what is inside, how much, where it belongs |
| Container labelled "rice" but with mixed contents | **Semi-structured** - some organisation, inconsistently applied |
| Unlabelled mystery bag at the back of the freezer | **Unstructured** - must open and investigate before you know what you have |

**DW reconnect:** A warehouse is built for structured data - the labelled Tupperware. ETL (Days 3–4) converts the other formats into correctly labelled, consistently portioned Tupperware before they enter the warehouse.

---

### 1.5 Dimensions of Data

Every analytical question involves one or more of these:

| Dimension | What it captures | DW table |
|-----------|-----------------|---------|
| **Time** | Historical vs real-time; which period? | `dim_date` |
| **Subject** | Which category of business activity? | `dim_course`, `dim_student` |
| **Location** | Geographic or organisational context | Campus, department |

**NIBM example:** Enrollment data has a *time* dimension (which semester), a *subject* dimension (which course), and a *location* dimension (which campus or delivery mode). These become your dimension tables on Day 2.

---

### 1.6 Big Data and the 5Vs

**Big data** refers to datasets too large or complex to manage with traditional tools.

| V | Definition | Concrete example |
|---|-----------|-----------------|
| **Volume** | Scale beyond what a single machine can hold or query quickly | Facebook processes over 100 billion events per day |
| **Velocity** | Speed at which new data arrives and decisions must be made | Stock exchange price ticks arriving milliseconds apart |
| **Variety** | Range of data types, formats, and sources arriving together | Video, text posts, location check-ins from one app |
| **Veracity** | Trustworthiness and accuracy of incoming data | A patient allergy field left blank vs filled "none" - both appear complete; only one is safe to act on |
| **Value** | The business insight or decision that justifies the effort | Netflix recommending a show you actually watch |

#### Analogy: The Ocean and the Harbour

| Part | Maps to |
|------|--------|
| A vast ocean | The totality of data generated globally every day |
| A rowing boat | Traditional tools - fine on a pond, completely inadequate on the ocean |
| A cargo ship with GPS and radar | Big data infrastructure - operates at scale, at speed |
| A port with organised warehouses | Your data warehouse - structured endpoint where data is received, verified, sorted, made accessible |

**Where it breaks:** An ocean is uniform (water). Big data is heterogeneous - structured, noisy, fast, slow - all at once.

---

### 1.7 Data Quality - The Six DAMA UK Dimensions

These dimensions are what ETL enforces before any row enters the warehouse. A violation means the row is rejected or flagged for review.

| # | Dimension | Definition | Example |
|---|-----------|-----------|---------|
| 1 | **Accuracy** | How well data represents real-world facts | Delivery service ensuring customer address is correct |
| 2 | **Completeness** | Proportion of stored data vs what should be available | Healthcare DB with all necessary patient records |
| 3 | **Uniqueness** | Data stored without unnecessary duplication | National ID appearing exactly once in government databases |
| 4 | **Consistency** | No conflict between data from multiple sources | Financial reports from different branches showing the same revenue figure |
| 5 | **Timeliness** | Data is up-to-date and available when required | Real-time public transportation schedule updates |
| 6 | **Validity** | Data conforms to defined business rules or formats | Customer phone numbers following valid format |

---

### 1.8 Metadata

**Metadata** is data about data - it provides context and meaning.

Types relevant to data warehousing:

| Type | What it describes | Example |
|------|------------------|---------|
| **Technical metadata** | Schema definitions, data types, table relationships | Column names, data types, FK constraints |
| **Business metadata** | What a field means in this specific organisation | What "revenue" means here - gross or net? |
| **Operational metadata** | When data was last loaded, and by which process | ETL run timestamp, row counts |

#### Analogy: The Library Catalogue

| Part | Maps to |
|------|--------|
| Catalogue card (title, author, shelf location, date) | Metadata - describes the data without you opening it |
| Looking up a book before walking to the shelf | Querying metadata before querying the data |
| The actual book | The data itself |

**Where it breaks:** A catalogue card is written once and rarely changes. Warehouse metadata must evolve continuously as tables, columns, and source systems change.

**DW reconnect:** Without metadata, a warehouse is a building with no labels on any shelf. The data is there, but no analyst can locate or trust what they retrieve.

---

## Part 2 - Foundations: Why a Warehouse Exists (Session 6.1.B)

### 2.1 Decision Support Systems

A **Decision Support System (DSS)** is a computer-based system that helps people make informed decisions - particularly for complex problems with incomplete information.

Every DSS has three layers:

```
COLLECT             TRANSFORM & STORE           ANALYSE & DECIDE
OLTP systems   →    ETL → Data Warehouse    →    OLAP / BI Tools
sensors, files
```

This module teaches you to build the **middle layer** (the warehouse) and use the **right layer** (Metabase) to visualise it.

---

### 2.2 Evolution of Decision Support

| Decade | System | What changed |
|--------|--------|-------------|
| 1960s | **MIS** | Mainframe reports - fixed, periodic, batch-produced. No interactivity. |
| 1970s | **DSS** | Interactive query tools. Managers could ask questions directly for the first time. |
| 1980s | **EIS** | Executive dashboards. Colour displays. Pre-built KPI views for senior management. |
| 1990s | **Data Warehouse** | Inmon (1990), Kimball (1996). Integrated, historical, purpose-built for analysis. |
| 2000s+ | **OLAP & Self-Service BI** | Slice, dice, drill-down. Excel pivot tables, then dedicated BI tools. |

We are building a 1990s-style dimensional warehouse - still the dominant pattern in production systems today.

---

### 2.3 OLTP vs OLAP

#### OLTP - Online Transaction Processing

**Designed to record business events accurately and fast - one at a time.**

- Handles INSERT, UPDATE, DELETE
- Optimised for many short, concurrent transactions
- Schema is **normalised (3NF)**: data stored once, relationships via foreign keys
- Contains **current state only**
- Our example: the NIBM `enrollments` table records each student–course–semester combination as it happens

#### OLAP - Online Analytical Processing

**Designed to answer complex questions across large volumes of historical data.**

- Handles SELECT with complex aggregates and GROUP BY
- Optimised for large scans across millions of rows
- Schema is **denormalised (star/snowflake)**: redundancy is acceptable because we never update
- Contains **historical snapshots** across years
- Our example: `fact_enrollment` will hold every enrollment event ever recorded

#### Side-by-Side Comparison

| | OLTP | OLAP / Data Warehouse |
|--|------|----------------------|
| **Purpose** | Record business events | Analyse historical patterns |
| **Operation** | INSERT / UPDATE / DELETE | SELECT with GROUP BY, aggregates |
| **Schema** | Normalised (3NF) | Denormalised (star schema) |
| **Data scope** | Current state only | Years of historical snapshots |
| **Users** | Applications, clerks, systems | Analysts, managers, BI tools |
| **Query speed** | Milliseconds (one row) | Seconds (millions of rows) |
| **NIBM example** | Record one enrollment | "How many enrolled per course this year?" |

---

### 2.4 Analogy: Cash Register vs Year-End Accountant (OLTP vs OLAP)

| Part | Content |
|------|---------|
| **Familiar concept** | A busy supermarket checkout till vs. the company accountant reviewing the year-end ledger |
| **Structural mapping** | The cash register = OLTP: records one sale at a time, fast and accurately. It cannot tell you total revenue for the year - it only knows this transaction. The accountant = OLAP: works across the entire year's records, aggregates totals, spots trends. They do not ring individual sales - they analyse. |
| **Where it breaks** | A real accountant reads directly from the same records the till produced. In a warehouse, we copy and transform data into a separate, redesigned store. The accountant's ledger is purpose-built - that is the data warehouse. |
| **DW reconnect** | Your PostgreSQL OLTP DB is the cash register. The `fact_enrollment` table we build on Day 2 is the ledger. The warehouse exists so the analyst never slows down the operational system. |

---

### 2.5 Inmon's Four Properties of a Data Warehouse

Bill Inmon (1990): *"A data warehouse is a **subject-oriented, integrated, non-volatile, and time-variant** collection of data in support of management's decision making."*

| Property | Definition | NIBM enrollment example |
|----------|-----------|------------------------|
| **Subject-oriented** | Organised around business subjects (students, courses, revenue) - not around processes | We organise around *students* and *courses*, not around the *enrollment process* |
| **Integrated** | Combines data from multiple source systems into one consistent view | OLTP enrollment data + HR system for instructors + finance system |
| **Non-volatile** | Data is loaded but rarely deleted or updated - the historical record is preserved | We do not delete last year's enrollments; they remain for trend analysis |
| **Time-variant** | Every record carries a time reference; the warehouse holds snapshots across years | We can compare Semester 1, 2020 with Semester 1, 2024 |

---

### 2.6 Warehouse vs Database - What Changes

| | Operational Database (OLTP) | Data Warehouse (OLAP) |
|--|----------------------------|----------------------|
| **Design goal** | Record transactions | Analyse historical patterns |
| **Schema** | Normalised - avoid all redundancy | Denormalised - optimise for reads |
| **Data age** | Current state only | Years of historical snapshots |
| **Query pattern** | Many small concurrent reads and writes | Few large reads scanning millions of rows |
| **Update method** | Updated row-by-row as events happen | Loaded in batches via ETL |
| **Example question** | "Is student S001 enrolled?" | "How has enrolment changed over 5 years?" |

---

### 2.7 Analogy: Library Circulation vs Reference Section (Warehouse vs Database)

| Part | Content |
|------|---------|
| **Familiar concept** | A university library - the circulation desk vs. the reference section |
| **Structural mapping** | Circulation desk = operational DB: fast, transactional, one borrow or return at a time, optimised for throughput. Reference section = data warehouse: you sit, browse, cross-reference across many sources, take your time. Optimised for depth of inquiry. |
| **Where it breaks** | A reference section holds physical books - static content. A warehouse is queried dynamically. The "books" are the records; the query is how you navigate and combine them at runtime. |
| **DW reconnect** | DBeaver showing you the OLTP schema is the circulation desk - fast record access. The star schema we build on Day 2 is the reference section - structured for analytical inquiry. Both live in PostgreSQL; the architecture is what changes. |

---

### 2.8 Key Concept: Denormalisation is a Deliberate Choice, Not a Mistake

In an operational database, **redundancy is a problem** - it causes update anomalies and inconsistency.

In a data warehouse, **we never update individual rows**. We load, and we read. Redundancy does not cause inconsistency if the data never changes after load.

**Denormalisation means pre-joining:** the dimension tables store descriptive attributes so the analyst does not have to JOIN across four normalised tables every time they ask a question.

---

## Part 3 - The Schemas in DBeaver

### 3.1 OLTP Schema (Normalised - 4 tables)

```
┌─────────────┐     ┌─────────────────────┐     ┌─────────────┐
│  students   │     │     enrollments      │     │   courses   │
│─────────────│     │─────────────────────│     │─────────────│
│ student_id  │◄────│ student_id     FK   │     │ course_id   │
│ first_name  │     │─────────────────────│────►│ course_name │
│ last_name   │     │ enrollment_id  PK   │     │ department  │
│ email       │     │ course_id      FK   │     │ credits     │
│ date_of_b.  │     │ instructor_id  FK   │     │ level       │
│ nationality │     │ enrollment_date     │     └─────────────┘
└─────────────┘     │ grade               │
                    │ semester            │     ┌─────────────┐
                    │ academic_year       │     │ instructors │
                    └─────────────────────│────►│─────────────│
                                          │     │instructor_id│
                                          │     │ first_name  │
                                          │     │ last_name   │
                                          │     │ department  │
                                          │     └─────────────┘
```

**Rule in OLTP:** each piece of data is stored exactly once. Student names live in `students`, course names in `courses`. No duplication.

---

### 3.2 OLTP Query - Analytical Question (3 table JOIN)

Business question: *"How many students enrolled in each course in Semester 1, 2024?"*

```sql
SELECT c.course_name,
       COUNT(e.enrollment_id) AS enrolled_count
FROM   enrollments e
JOIN   students  s  ON e.student_id = s.student_id
JOIN   courses   c  ON e.course_id  = c.course_id
WHERE  e.semester      = 'Semester 1'
  AND  e.academic_year = '2024'
GROUP  BY c.course_name
ORDER  BY enrolled_count DESC;
```

**Steps the database takes:**
1. Scan `enrollments` for the matching semester and year
2. JOIN to `students` (match `student_id`) - even though we don't need student attributes
3. JOIN to `courses` (match `course_id`) - to get `course_name`
4. GROUP BY `course_name` and COUNT

Every time this query runs on growing OLTP data, all three tables are touched. The JOIN cost grows as the database grows.

---

### 3.3 DW Schema (Star - 4 tables, zero rows today)

```
                    ┌──────────────┐
                    │  dim_student │
                    │──────────────│
                    │ student_key  │
                    │ student_id   │
                    │ full_name    │
                    │ nationality  │
                    │ cohort_year  │
                    └──────┬───────┘
                           │ student_key
                           ▼
┌─────────────┐   ┌──────────────────┐   ┌─────────────┐
│  dim_course │   │ fact_enrollment  │   │  dim_date   │
│─────────────│   │──────────────────│   │─────────────│
│ course_key  │◄──│ course_key   FK  │   │ date_key    │
│ course_name │   │ student_key  FK  │   │ full_date   │
│ department  │   │ date_key     FK  │──►│ semester    │
│ credits     │   │ enrollment_key PK│   │ acad_year   │
└─────────────┘   │ enrollment_date  │   │ week        │
                  │ grade            │   └─────────────┘
                  │ semester         │
                  └──────────────────┘
```

**Rule in DW:** dimension tables store descriptive attributes. The fact table stores foreign keys and numeric measures. This is the star schema pattern.

---

### 3.4 DW Query - Same Question (2 key JOINs)

```sql
SELECT dc.course_name,
       COUNT(fe.enrollment_key) AS enrolled_count
FROM   fact_enrollment fe
JOIN   dim_course dc ON fe.course_key = dc.course_key
JOIN   dim_date   dd ON fe.date_key   = dd.date_key
WHERE  dd.semester      = 'Semester 1'
  AND  dd.academic_year = '2024'
GROUP  BY dc.course_name
ORDER  BY enrolled_count DESC;
```

**Steps the database takes:**
1. Scan `fact_enrollment` with pre-keyed integer keys (fast)
2. JOIN `dim_course` (integer key lookup - very fast)
3. JOIN `dim_date` to filter semester (small table - very fast)
4. GROUP BY `course_name` and COUNT

**Both queries return identical results.** The DW query is faster at scale because the schema is designed for this type of access.

---

## Part 4 - Lab Notes

### 4.1 Comparing the Two Approaches

After running both queries in DBeaver, note:

| | OLTP Query | DW Query |
|--|-----------|---------|
| Tables touched | 3 | 3 |
| JOIN type | String FK (normalised) | Integer surrogate key |
| Need student data? | Yes (JOIN required) | No (course name is in dim_course) |
| Simulated time | 2,000ms | 400ms |
| SQL complexity | Higher (3 JOINs incl. students) | Lower (2 JOINs, pre-organised) |

**Key insight:** The DW does not need to JOIN to `dim_student` to answer this question because `course_name` is already in `dim_course`. In OLTP, every JOIN traverses the normalised structure even when the attribute is not needed in the output.

---

### 4.2 Requirements Activity - Worked Example

**Manager says:** "I need to understand enrollment trends - are we growing overall? Are certain courses shrinking? Which departments are performing?"

**Translating into three specific questions:**

| # | Business question | Data needed | Shape of answer | Why not OLTP? |
|---|------------------|------------|-----------------|--------------|
| 1 | How has total enrollment changed each semester for the past 3 years? | `fact_enrollment` grouped by `dim_date.semester + academic_year` | Time-series line chart | OLTP contains current data; historical comparison requires a warehouse |
| 2 | Which 5 courses have the largest semester-over-semester decline in enrollment? | `fact_enrollment` + `dim_course` + `dim_date`; compare periods | Ranked table with trend arrows | OLTP JOIN across three tables for each period comparison; expensive at scale |
| 3 | What is the average enrollment per course by department? | `fact_enrollment` + `dim_course.department` + `dim_date` filter | Bar chart by department | Requires aggregation across years; OLTP not designed for multi-year reads |

---

## Part 5 - Day 1 Deliverable Template

**Hand in or photograph before leaving.** Complete one section per business question.

---

**Scenario:** NIBM university enrollment data - students, courses, semesters, academic years.

---

**Business Question 1:**
> _______________________________________________

Data needed:
> Table(s): _______________ Columns: _______________ Time range: _______________

Shape of the answer:
> ☐ Single number  ☐ Ranked list  ☐ Time-series comparison  ☐ Breakdown by category

Why a warehouse (not just the OLTP DB)?
> _______________________________________________

---

**Business Question 2:**
> _______________________________________________

Data needed:
> Table(s): _______________ Columns: _______________ Time range: _______________

Shape of the answer:
> ☐ Single number  ☐ Ranked list  ☐ Time-series comparison  ☐ Breakdown by category

Why a warehouse?
> _______________________________________________

---

**Business Question 3:**
> _______________________________________________

Data needed:
> Table(s): _______________ Columns: _______________ Time range: _______________

Shape of the answer:
> ☐ Single number  ☐ Ranked list  ☐ Time-series comparison  ☐ Breakdown by category

Why a warehouse?
> _______________________________________________

---

## Part 6 - Key Terms Glossary

| Term | Definition |
|------|-----------|
| **Data** | Raw facts and figures - unprocessed, uncontextualised |
| **Information** | Data with context and meaning |
| **Knowledge** | Understanding gained from information; the ability to act |
| **Structured data** | Data in a consistently organised format (SQL tables) |
| **Unstructured data** | Data with no predefined format (images, text) |
| **Semi-structured data** | Data with inconsistent or partial structure (JSON, PDF) |
| **Metadata** | Data about data - describes context, origin, format |
| **Big data** | Datasets too large or complex for traditional tools |
| **5Vs** | Volume, Velocity, Variety, Veracity, Value |
| **DAMA UK** | Data Management Association - publisher of the 6 data quality dimensions |
| **DSS** | Decision Support System - computer system that aids decision-making |
| **OLTP** | Online Transaction Processing - records operational events |
| **OLAP** | Online Analytical Processing - analyses historical data |
| **Data warehouse** | Integrated, historical, non-volatile, subject-oriented data store for analysis |
| **Normalisation** | Database design principle: store each fact exactly once |
| **Denormalisation** | Pre-joining data for faster analytical reads; acceptable in a warehouse because data is non-volatile |
| **Star schema** | DW design: one central fact table surrounded by dimension tables |
| **Fact table** | Central table storing measures (counts, amounts) and foreign keys |
| **Dimension table** | Surrounding tables storing descriptive attributes (names, categories, dates) |
| **ETL** | Extract, Transform, Load - the process of moving data from OLTP to warehouse |
| **Surrogate key** | Integer key generated by the warehouse; replaces the operational system's natural key |
| **Grain** | The level of detail stored in each row of the fact table (e.g., one row per enrollment) |
| **Inmon** | Bill Inmon - coined the data warehouse definition in 1990 |
| **Kimball** | Ralph Kimball - developed the dimensional modelling approach in 1996 |
| **DBeaver** | Open-source SQL client and ERD viewer used in this module |
| **PostgreSQL** | Open-source relational database engine used for both OLTP and DW in this module |

---

## What's Next - Day 2

Tomorrow we build the star schema.

**Morning:** dimensional modelling theory - facts vs dimensions, grain, star vs snowflake.

**Afternoon lab:**
1. Write the DDL for `fact_enrollment`, `dim_student`, `dim_course`, `dim_date`
2. Load seed data via `INSERT`
3. Run your first analytical query against the populated warehouse
4. View the ERD auto-drawn in DBeaver

The empty schema you saw today will have data by end of Day 2.

---

*Data Warehousing and Business Intelligence*
*NIBM / Coventry University - BSc (Hons) Computing & HND Software Engineering - Level 4*
*Niranga Dharmaratna - niranga@nibm.lk*
