# RaceDay API Endpoint Plan

Roles referenced below: **None** (public, no login required), **Any** (any authenticated user, either role), **Organiser**, **Participant**.

## Authentication

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| POST | /api/auth/register | Creates a new user account as either an Organiser or a Participant and hashes the supplied password. | None | { fullName, email, password, role, phoneNumber } | 201 Created – user record (no password returned) / 400 Bad Request – validation failed / 409 Conflict – email already registered |
| POST | /api/auth/login | Validates credentials, creates a session, and returns the user's role. | None | { email, password } | 200 OK – session established, user id and role returned / 401 Unauthorized – invalid credentials |
| POST | /api/auth/logout | Ends the current user's session. | Any | None | 200 OK – session cleared / 401 Unauthorized – no active session |

## User Profile

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/profile | Returns the logged-in user's own profile details. | Any | None | 200 OK – profile object / 401 Unauthorized – not logged in |
| PUT | /api/profile | Updates the logged-in user's own profile details. | Any | { fullName, phoneNumber } | 200 OK – updated profile / 400 Bad Request – validation failed / 401 Unauthorized |

## Events

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/events | Lists all upcoming events. Both roles can browse events. | None | None | 200 OK – array of events |
| GET | /api/events/{id} | Returns the full detail for a single event. | None | None | 200 OK – event object / 404 Not Found |
| POST | /api/events | Creates a new event. | Organiser | { name, description, eventDate, location, distanceKm, eventType } | 201 Created – new event / 400 Bad Request / 401 Unauthorized / 403 Forbidden |
| PUT | /api/events/{id} | Updates an existing event owned by the logged-in Organiser. | Organiser | { name, description, eventDate, location, distanceKm, eventType } | 200 OK – updated event / 404 Not Found / 403 Forbidden (not the owner) |
| DELETE | /api/events/{id} | Deletes an event owned by the logged-in Organiser. | Organiser | None | 204 No Content / 404 Not Found / 403 Forbidden |

## Categories

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/events/{eventId}/categories | Lists all categories available for a specific event. | None | None | 200 OK – array of categories / 404 Not Found (event does not exist) |
| POST | /api/events/{eventId}/categories | Adds a new age or distance category to an event. | Organiser | { categoryName, minAge, maxAge, distanceKm } | 201 Created – new category / 403 Forbidden (not the event owner) |
| PUT | /api/categories/{id} | Updates an existing category. | Organiser | { categoryName, minAge, maxAge, distanceKm } | 200 OK – updated category / 404 Not Found / 403 Forbidden |
| DELETE | /api/categories/{id} | Removes a category from an event. | Organiser | None | 204 No Content / 404 Not Found / 403 Forbidden |

## Event Enrolments

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| POST | /api/events/{eventId}/enrolments | Enrols the logged-in Participant into an event under a chosen category. | Participant | { categoryId } | 201 Created – enrolment record with status "Pending" / 404 Not Found (event/category) / 409 Conflict (already enrolled) |
| GET | /api/enrolments/my | Lists all events the logged-in Participant has enrolled for. | Participant | None | 200 OK – array of enrolments |
| GET | /api/events/{eventId}/enrolments | Lists all Participants enrolled in a specific event, owned by the Organiser. | Organiser | None | 200 OK – array of enrolments / 403 Forbidden (not the owner) |
| DELETE | /api/enrolments/{id} | Cancels the logged-in Participant's own enrolment. | Participant | None | 204 No Content / 404 Not Found / 403 Forbidden (not the owner) |

## Results

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| POST | /api/enrolments/{enrolmentId}/result | Captures the finish time and finishing position for a Participant after the event. | Organiser | { finishTime, finishPosition } | 201 Created – result record / 404 Not Found (enrolment) / 403 Forbidden (not event owner) / 409 Conflict (result already recorded) |
| GET | /api/results/my | Returns the logged-in Participant's full personal result history. | Participant | None | 200 OK – array of results with event name, date, category, finish time, and position |
| GET | /api/events/{eventId}/results | Returns all published results for a specific event. | None | None | 200 OK – array of results / 404 Not Found |
