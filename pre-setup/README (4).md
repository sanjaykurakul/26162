# Campus Evaluation – Backend

Backend submission for the campus evaluation exercise. This repository is identified by roll number only; no evaluator or organizer name appears anywhere in this repo, its commit history, or its documentation.

## Status

Pre-setup phase complete. Task-specific backend requirements and implementation will be added in a follow-up commit.

## Pre-Setup Steps Completed

### 1. Registration

A one-time registration call was made to obtain a `clientID` and `clientSecret`.

```
POST /evaluation-service/register
Content-Type: application/json

{
  "email": "<college-email>",
  "name": "<name>",
  "mobileNo": "<mobile-number>",
  "githubUsername": "<github-username>",
  "rollNo": "<roll-number>",
  "accessCode": "<access-code-from-email>"
}
```

Response:

```
{
  "clientID": "<uuid>",
  "clientSecret": "<secret>"
}
```

These values are saved locally and are **not** committed to this repository.

### 2. Auth Token

An access token is requested using the registration details plus the issued `clientID` / `clientSecret`.

```
POST /evaluation-service/auth
Content-Type: application/json

{
  "email": "<college-email>",
  "name": "<name>",
  "rollNo": "<roll-number>",
  "accessCode": "<access-code-from-email>",
  "clientID": "<clientID>",
  "clientSecret": "<clientSecret>"
}
```

Response:

```
{
  "token_type": "Bearer",
  "access_token": "<jwt>",
  "expires_in": <unix-timestamp>
}
```

The token is short-lived and is refreshed by calling this endpoint again on expiry (HTTP 401 from a protected route signals this).

### 3. Logging Middleware

A reusable logging function is integrated throughout the codebase instead of ad-hoc `console.log`/`print` calls:

```
Log(stack, level, package, message)
```

Internally this calls the protected log API with a Bearer token attached:

```
POST /evaluation-service/logs
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "stack": "backend",
  "level": "info",
  "package": "handler",
  "message": "<description of event>"
}
```

**Allowed values**

| Field | Allowed values |
|---|---|
| `stack` | `backend`, `frontend` |
| `level` | `debug`, `info`, `warn`, `error`, `fatal` |
| `package` (backend-specific) | `cache`, `controller`, `cron_job`, `db`, `domain`, `handler`, `repository`, `route`, `service` |
| `package` (shared) | `auth`, `config`, `middleware`, `utils` |

All values are lowercase.

## Verification

The pre-setup was verified end-to-end against the live evaluation service:

| Check | Result |
|---|---|
| Registration | Success — `clientID`/`clientSecret` issued |
| Auth | Success — Bearer token issued |
| Log API | Success — log entry created and acknowledged with a `logID` |

## Project Structure (placeholder — to be finalized with the actual task)

```
.
├── src/
│   ├── controller/
│   ├── service/
│   ├── repository/
│   ├── db/
│   ├── middleware/
│   │   └── logger.js        # Log(stack, level, package, message)
│   ├── route/
│   ├── utils/
│   └── config/
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

## Environment Variables

Credentials are kept out of source control via environment variables (see `.env.example`):

```
CLIENT_ID=
CLIENT_SECRET=
EMAIL=
NAME=
ROLL_NO=
ACCESS_CODE=
```

## Constraints Followed

- No external libraries used for core algorithm logic.
- No reference to the organizing company's name anywhere in the repo, README, or commit history.
- Repository name is the roll number only.

## Next Steps

Backend task requirements are pending. Once received, this README will be updated with:

- Functional requirements and chosen approach
- API endpoint documentation for the implemented service
- Setup/run instructions
- Postman/Insomnia evidence (request, response, response time) per submission rules
