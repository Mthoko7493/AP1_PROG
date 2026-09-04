System Description

RaceDay is a full-stack web-based event management system built for the South African road running, walking, and cycling community. The platform lets Event Organisers create and manage events, define categories, and capture participant results, while Participants can browse upcoming events, enter events by selecting a category, and track their personal performance history.
This part of the Portfolio of Evidence covers the planning phase only — no application code is included. It contains the Entity Relationship Diagram, the API endpoint plan, and the SQL database script that Part 2 (the RESTful API) will be built against.
User Roles
Role	Description
Organiser	Can create, edit, and delete events; manage event categories; capture participant results; and view all enrolments for their events.
Participant	Can create an account, browse events, enter an event by selecting a category, view their own enrolments, and track their personal results.
/docs
  RaceDay_ERD.png                     — Entity Relationship Diagram (image)
  RaceDay_ERD.pdf                     — Entity Relationship Diagram (PDF)
  RaceDay_API_Endpoint_Plan.docx      — Full API endpoint plan (Word)
  API_Endpoint_Plan.md                — Full API endpoint plan (Markdown)
  RaceDay_Database.sql                — SQL Server database creation + seed script
README.md                             — this file
Database Script — Setup Instructions
Open SQL Server Management Studio (SSMS) and connect to a local or clean SQL Server instance
Open docs/RaceDay_Database.sql.
Execute the script (F5). It will:
Create the RaceDayDB database if it does not already exist.
Drop and recreate all tables (safe to re-run).
Seed the database with 2 Organisers, 2 Participants, 3 Events, 6 Categories, 3 Enrolments, and 2 Results.
Verify the tables under RaceDayDB > Tables and spot-check the seed data with a SELECT * FROM dbo.Events;.
Entity Relationship Diagram

The ERD (docs/RaceDay_ERD.png / .pdf) models six entities — Roles, Users, Events, Categories, Enrolments, and Results — with all primary keys, foreign keys, and cardinality shown. It matches the SQL script exactly.
CI/CD

[Insert screenshot of your successful green GitHub Actions build here before submission.]

The GitHub Actions workflow (.github/workflows/) validates that the /docs folder exists and contains the required planning files.
Video Presentation

[Insert unlisted YouTube link here.]

The video walks through the planning documents, explains the ERD design decisions and endpoint plan choices, and runs the SQL script live in SSMS.
