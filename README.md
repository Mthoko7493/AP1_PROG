RaceDay — Part 1: System Planning and Database
Module: PROG6212/w — Programming 2B
Student Name: [Your Full Name]
Student Number: [Your Student Number]
System Description
RaceDay is a full-stack web-based event management system built for the South African road running, walking, and cycling community. The platform lets Event Organisers create and manage events, define categories, and capture participant results, while Participants can browse upcoming events, enter events by selecting a category, and track their personal performance history.
This part of the Portfolio of Evidence covers the planning phase only — no application code is included. It contains the Entity Relationship Diagram, the API endpoint plan, and the SQL database script that Part 2 (the RESTful API) will be built against.
User Roles
Role	Description
Organiser	Can create, edit, and delete events; manage event categories; capture participant results; and view all enrolments for their events.
Participant	Can create an account, browse events, enter an event by selecting a category, view their own enrolments, and track their personal results.
Repository Structure
```
/docs
  RaceDay_ERD.png                     — Entity Relationship Diagram (image)
  RaceDay_ERD.pdf                     — Entity Relationship Diagram (PDF)
  RaceDay_API_Endpoint_Plan.docx      — Full API endpoint plan (Word)
  API_Endpoint_Plan.md                — Full API endpoint plan (Markdown)
  RaceDay_Database.sql                — SQL Server database creation + seed script
README.md                             — this file
```
Database Script — Setup Instructions
Open SQL Server Management Studio (SSMS) and connect to a local or clean SQL Server instance.
Open `docs/RaceDay_Database.sql`.
Execute the script (`F5`). It will:
Create the `RaceDayDB` database if it does not already exist.
Drop and recreate all tables (safe to re-run).
Seed the database with 2 Organisers, 2 Participants, 3 Events, 6 Categories, 3 Enrolments, and 2 Results.
Verify the tables under `RaceDayDB > Tables` and spot-check the seed data with a `SELECT * FROM dbo.Events;`.
Entity Relationship Diagram
The ERD (`docs/RaceDay_ERD.png` / `.pdf`) models six entities — Roles, Users, Events, Categories, Enrolments, and Results — with all primary keys, foreign keys, and cardinality shown. It matches the SQL script exactly.
CI/CD
[Insert screenshot of your successful green GitHub Actions build here before submission.]
The GitHub Actions workflow (`.github/workflows/`) validates that the `/docs` folder exists and contains the required planning files.
Video Presentation
[Insert unlisted YouTube link here.]

Roles referenced below: None (public, no login required), Any (any authenticated user, either role), Organiser, Participant.
The video walks through the planning documents, explains the ERD design decisions and endpoint plan choices, and runs the SQL script live in SSMS.
AI Tool Disclosure
AI assistance (Claude, Anthropic) was used during the planning phase to help draft and structure the ERD, API endpoint plan, and SQL script. All content was reviewed, and will be explained in my own words in the video 

Authentication
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
POST	/api/auth/register	Creates a new user account as either an Organiser or a Participant and hashes the supplied password.	None	{ fullName, email, password, role, phoneNumber }	201 Created – user record (no password returned) / 400 Bad Request – validation failed / 409 Conflict – email already registered
POST	/api/auth/login	Validates credentials, creates a session, and returns the user's role.	None	{ email, password }	200 OK – session established, user id and role returned / 401 Unauthorized – invalid credentials
POST	/api/auth/logout	Ends the current user's session.	Any	None	200 OK – session cleared / 401 Unauthorized – no active session
User Profile
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/profile	Returns the logged-in user's own profile details.	Any	None	200 OK – profile object / 401 Unauthorized – not logged in

User Profile
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/profile	Returns the logged-in user's own profile details.	Any	None	200 OK – profile object / 401 Unauthorized – not logged in
PUT	/api/profile	Updates the logged-in user's own profile details.	Any	{ fullName, phoneNumber }	200 OK – updated profile / 400 Bad Request – validation failed / 401 Unauthorized
PUT	/api/profile	Updates the logged-in user's own profile details.presentation.
