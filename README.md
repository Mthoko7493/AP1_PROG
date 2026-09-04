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

Events
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/events	Lists all upcoming events. Both roles can browse events.	None	None	200 OK – array of events
GET	/api/events/{id}	Returns the full detail for a single event.	None	None	200 OK – event object / 404 Not Found
POST	/api/events	Creates a new event.	Organiser	{ name, description, eventDate, location, distanceKm, eventType }	201 Created – new event / 400 Bad Request / 401 Unauthorized / 403 Forbidden
PUT	/api/events/{id}	Updates an existing event owned by the logged-in Organiser.	Organiser	{ name, description, eventDate, location, distanceKm, eventType }	200 OK – updated event / 404 Not Found / 403 Forbidden (not the owner)
DELETE	/api/events/{id}	Deletes an event owned by the logged-in Organiser.	Organiser	None	204 No Content / 404 Not Found / 403 Forbidden
GET	/api/events/search	Searches and filters events by event type, location, or date range.	None	None (query params: type, location, fromDate, toDate)	200 OK – array of matching events
Categories
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/events/{eventId}/categories	Lists all categories available for a specific event.	None	None	200 OK – array of categories / 404 Not Found (event does not exist)
POST	/api/events/{eventId}/categories	Adds a new age or distance category to an event.	Organiser	{ categoryName, minAge, maxAge, distanceKm }	201 Created – new category / 403 Forbidden (not the event owner)
PUT	/api/categories/{id}	Updates an existing category.	Organiser	{ categoryName, minAge, maxAge, distanceKm }	200 OK – updated category / 404 Not Found / 403 Forbidden
DELETE	/api/categories/{id}	Removes a category from an event.	Organiser	None	204 No Content / 404 Not Found / 403 Forbidden
Event Enrolments
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
POST	/api/events/{eventId}/enrolments	Enrols the logged-in Participant into an event under a chosen category.	Participant	{ categoryId }	201 Created – enrolment record with status "Pending" / 404 Not Found (event/category) / 409 Conflict (already enrolled)
GET	/api/enrolments/my	Lists all events the logged-in Participant has enrolled for.	Participant	None	200 OK – array of enrolments
GET	/api/events/{eventId}/enrolments	Lists all Participants enrolled in a specific event, owned by the Organiser.	Organiser	None	200 OK – array of enrolments / 403 Forbidden (not the owner)
DELETE	/api/enrolments/{id}	Cancels the logged-in Participant's own enrolment.	Participant	None	204 No Content / 404 Not Found / 403 Forbidden (not the owner)
PUT	/api/enrolments/{id}/status	Updates the status of a Participant's enrolment (e.g. Pending to Confirmed) for an event the Organiser owns.	Organiser	{ status }	200 OK – updated enrolment / 404 Not Found / 403 Forbidden (not the event owner)
Results
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
POST	/api/enrolments/{enrolmentId}/result	Captures the finish time and finishing position for a Participant after the event.	Organiser	{ finishTime, finishPosition }	201 Created – result record / 404 Not Found (enrolment) / 403 Forbidden (not event owner) / 409 Conflict (result already recorded)
GET	/api/results/my	Returns the logged-in Participant's full personal result history.	Participant	None	200 OK – array of results with event name, date, category, finish time, and position
GET	/api/events/{eventId}/results	Returns all published results for a specific event.	None	None	200 OK – array of results / 404 Not Found
GET	/api/events/{eventId}/leaderboard	Returns the full ranked leaderboard (all finishers, ordered by position) for a completed event.	None	None	200 OK – ordered array of results / 404 Not Found
Organiser Dashboard
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/organiser/dashboard	Returns a summary for the logged-in Organiser: their events, total enrolments per event, and upcoming event dates.	Organiser	None	200 OK – dashboard summary object / 401 Unauthorized
Weather & Route Information
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/events/{eventId}/weather	Returns the live weather forecast for an event's location and date, to help Participants prepare for race day.	None	None	200 OK – weather forecast object / 404 Not Found (event) / 502 Bad Gateway (weather provider unavailable)
Content

PDF
HTTP Method	Route	Description	Role Required	Request Body	Expected Response
GET	/api/profile	Returns the logged-in user's own profile details.	Any	None	200 OK – profile object / 401 Unauthorized – not logged in
PUT	/api/profile	Updates the logged-in user's own profile details.	Any	{ fullName, phoneNumber }	200 OK – updated profile / 400 Bad Request – validation failed / 401 Unauthorized
PUT	/api/profile	Updates the logged-in user's own profile details.presentation.
