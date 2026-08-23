# Data Warehousing and Business Intelligence

Course materials for the Data Warehousing and Business Intelligence module at the National Institute of Business Management (NIBM), in association with Coventry University.

**Programmes:** BSc (Hons) Computing, Level 4 · Higher National Diploma in Software Engineering
**Module author:** Niranga Dharmaratna · niranga@nibm.lk

**Interactive visual aids:** https://nirangadh.github.io/Data-Warehousing-and-Business-Intelligence/

---

## What is in this repository

Everything for the module except the tools themselves. Slides, notes, lab data, notebooks and SQL scripts live here. The tools are on the USB drive, because they are too large for a repository.

Each day has its own folder. Once a day is published, its folder holds the same things every time.

| Folder | What is in it |
|---|---|
| (top level) | The slide decks as PDF, and the student notes as Markdown |
| `data/` | The files the lab reads |
| `notebooks/` | The Jupyter notebook you work in, and the completed version |
| `sql/` | SQL scripts you can run instead of typing |
| `reference/` | The correct output of that day |

**Days 1 and 2 are the exception right now.** They hold their decks and notes only. The four folders above are added to them in a later update. Every other published day has all four from the start.

**About `reference/`.** Copy the file from `reference/` if your lab result is wrong, or if you were absent. You are allowed to do this. It costs you no marks. It exists so one bad result does not block the rest of your week.

---

## How to use each day

Follow this order. It works better than reading the slides alone.

1. Open the student notes for the day. Read section 1, **Before You Start**, and section 3, **Key Words for Today**.
2. Read the theory deck. It is the first PDF in the day folder, marked `_A_`.
3. Open that day's visual aid from the coverage table below. It shows the same idea moving.
4. Work through the lab with the lab deck, marked `_B_`, and section 5 of the notes.
5. Answer the **Self-Check** questions at the end of the notes. The answers are included.
6. Save your deliverable using the exact filename the notes give you.

Every lab step tells you three things: what to run, what you should see, and what to do when it does not work. Stop and fix the step if what you see does not match.

New words are collected in [glossary.md](glossary.md). Each word is tagged with the day it was introduced.

---

## Coverage

| Day | Topic | Learning outcome | Slides | Notes | Visual aid | Status |
|---|---|---|---|---|---|---|
| 1 | Foundations: why a warehouse exists | LO1 | [Theory](Day1/DWBI_D1_A_Data_Concepts.pdf) · [Lab](Day1/DWBI_D1_B_Foundations.pdf) | [Notes](Day1/d1_student_notes.md) | [OLTP and DW compared](https://nirangadh.github.io/Data-Warehousing-and-Business-Intelligence/docs/d1_html_oltp_vs_dw.html) | Published |
| 2 | Dimensional modelling: build the spine | LO2 | [Theory](Day2/DWBI_D2_A_Theory.pdf) · [Lab](Day2/DWBI_D2_B_Labs.pdf) | [Notes](Day2/d2_student_notes.md) | [Star and snowflake explorer](https://nirangadh.github.io/Data-Warehousing-and-Business-Intelligence/docs/d2_html_star_schema.html) | Published |
| 3 | ETL part 1: extract, cleanse, integrate, transform | LO2, LO3 | | | | Not yet published |
| 4 | ETL part 2: reduction and load, plus OLAP concepts | LO3, LO4 | | | | Not yet published |
| 5 | OLAP operations lab, plus introduction to BI | LO4, LO5 | | | | Not yet published |
| 6 | Data visualisation | LO5 | | | | Not yet published |
| 7 | Integrated capstone, e-commerce domain | LO1 to LO5 | | | | Not yet published |
| Exam prep | Consolidation and mock paper | LO1 to LO5 | | | | Not yet published |

Each row goes live on the day that material is released. Day 3 is the next one due.

---

## Learning outcomes

On completion of this module you should be able to:

1. Explain the concepts of data warehousing and decision support systems.
2. Apply the de-normalized concept of data modeling in data warehousing.
3. Use of Oracle BI / MS Power BI tools for building Data Warehouses.
4. Explain the fundamentals of OLAP Cube creation.
5. Use data visualization for business intelligence.

The wording above is quoted from the module descriptor, including its spelling.

**A note on outcome 3.** This module teaches that outcome using Metabase, which is free and open source. The workflow is the same one the named tools use: connect to the warehouse, model the data, then build the visuals. The skill transfers directly.

---

## Tools

Four tools, one job each. All four run from the USB drive. None needs administrator rights.

| Tool | What you use it for | First used |
|---|---|---|
| PostgreSQL, portable | The warehouse itself, and OLAP queries | Day 1 |
| DBeaver Community, portable | Writing SQL and viewing the diagram | Day 1 |
| Python with pandas, through Jupyter | Cleaning and loading data | Day 3 |
| Metabase Community | Charts and dashboards | Day 6 |

### Setting up

1. Copy the whole tools folder from the USB drive to your machine. Do not run it from the USB drive if you can avoid it. Local disk is faster.
2. Start PostgreSQL with the launcher script. Wait until it prints that it is ready.
3. Start DBeaver. Connect to the database using the details on your quick-start sheet.
4. Download this repository as a ZIP. Use the green **Code** button, then **Download ZIP**. Unzip it next to your tools folder.

Check that you copied the whole folder, and not just the program file, if a tool does not start. A missing subfolder is the most common cause.

---

## Assessment

| Component | Weight | Covers |
|---|---|---|
| Coursework: data warehouse implementation with BI | 50 percent | LO1 to LO5 |
| Written test, closed book | 50 percent | LO1 to LO5 |

The coursework brief and marking rubric are released by the Day 4 session.

---

## Reading

| Book | Authors | ISBN | Publisher |
|---|---|---|---|
| Data Mining: Concepts and Techniques | Han, Kamber, Pei | 978-0123814791 | Morgan Kaufmann |
| The Data Warehouse ETL Toolkit | Kimball, Caserta | 978-0764567575 | Wiley and Sons |

---

## Licence

Slides, notes and code in this repository are released under the MIT Licence. See [LICENSE](LICENSE).

Any dataset that a different licence covers is distributed on the USB drive instead. Its terms are recorded separately.

---

## Changes

[CHANGELOG.md](CHANGELOG.md) records every update to a published file. Check it if you are unsure whether you hold the current version of something.

---

*Version 1.0 · 23 August 2026*
