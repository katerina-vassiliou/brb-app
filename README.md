# Brb. Leave Booking System

![Booking time off as an employee, then approving it as a manager](docs/media/demo.gif)

A full-stack leave booking system, built as university coursework. It is not a
product and isn't being developed any further, but I've kept it public as a
record of the work.

## The brief

Build a leave booking system with a React front end and a back end of our own,
using React Router and Tailwind CSS, meeting WCAG accessibility guidelines and
working in an agile way.

## What it does

There are three roles, and each sees a different app.

- **Employees** see their remaining allowance, book time off from a dialog that
  shows how many days it uses and what will be left, and follow their requests
  in a searchable, filterable table. They can cancel a request while it is
  pending.
- **Managers** get a queue of pending requests from their direct reports to
  approve or decline, a team summary on the dashboard and a team calendar. They
  can book leave too.
- **Admins** see an organisation-wide summary, can approve any request, book on
  someone's behalf, add, edit and delete people, manage departments and job
  roles, and set public holidays from the team calendar.

Everyone can see their own profile and allowance, and change their password,
from Settings.

## How it meets the brief

**React Router.** Routes are declared in `src/routes.tsx`. Role-based guards
wrap the manager and admin screens, so a route is refused in the router as well
as by the API.

**Tailwind CSS.** Tailwind v4, with colour and type tokens in one `@theme` block
(see [`docs/tokens.md`](docs/tokens.md)). A unit test fails if a raw hex colour
turns up anywhere in `src/`.

**WCAG 2.2 AA.** Accessibility is tested rather than assumed. Cypress specs
check colour contrast, focus order, visible focus rings, touch target size,
reflow at 320px wide and text zoomed to 200%, and the app was audited against
the AA criteria.

**Agile.** The work was split into small features, each built on its own branch
and merged through a pull request (33 of them), with conventional commit
messages. CI type-checks, lints, checks formatting and runs the tests on every
push and pull request.

**Back end.** Express 5 and TypeORM on MySQL, with JWT authentication,
peppered password hashing, Helmet, rate limiting, and generic error responses
that follow OWASP guidance. The frontend only talks to the API through a
gateway layer.

## Built with

- **Frontend:** Vite, React 19, TypeScript (strict), Tailwind CSS v4, React
  Router 7, Vitest and React Testing Library, Cypress
- **Backend:** Express 5, TypeORM, MySQL, JWT, Jest

## Documentation

- [`docs/tokens.md`](docs/tokens.md): the design tokens and shared loading,
  empty and error states
- [`backend/architecture.mmd`](backend/architecture.mmd),
  [`backend/class-diagram.mmd`](backend/class-diagram.mmd) and
  [`backend/erd.mmd`](backend/erd.mmd): Mermaid diagrams of the back end
- [`backend/postman_collection.json`](backend/postman_collection.json): the API
  as a Postman collection

## Running it

## Prerequisites

- Node.js 22 or newer
- npm 10 or newer
- A running MySQL server (backend only)

## Repository layout

```
.
├── src/            React application
├── cypress/        End-to-end specs
├── backend/        Express API (own package.json, own npm install)
└── .env.example    Frontend environment template
```

The frontend and backend are separate npm packages. Each needs its own
`npm install`.

## Backend setup

The frontend expects the API on **port 3000**, so start the backend first.

```bash
cd backend
npm install
cp .env.example .env
```

Fill in `backend/.env`:

| Variable          | Purpose                                      |
| ----------------- | -------------------------------------------- |
| `DB_HOST`         | MySQL host, e.g. `127.0.0.1`                 |
| `DB_PORT`         | MySQL port, e.g. `3306`                      |
| `DB_USERNAME`     | MySQL user                                   |
| `DB_PASSWORD`     | MySQL password                               |
| `DB_NAME`         | Database name, e.g. `leave_booking`          |
| `PASSWORD_PEPPER` | Secret mixed into password hashing           |
| `JWT_SECRET_KEY`  | Secret used to sign and verify auth tokens   |
| `RATE_LIMIT_MAX`  | Requests allowed per 15 minutes, default 500 |

Create the database, then seed it and start the API:

```bash
npm run seed
npm run dev
```

The API listens on `http://localhost:3000`.

### Backend scripts

| Command                 | Description                     |
| ----------------------- | ------------------------------- |
| `npm run dev`           | Start with auto-reload          |
| `npm start`             | Start once                      |
| `npm run seed`          | Populate the database           |
| `npm test`              | Run the Jest suite              |
| `npm run test:coverage` | Run Jest with coverage          |
| `npm run typecheck`     | Type-check without emitting     |
| `npm run lint`          | Run ESLint                      |
| `npm run format`        | Format with Prettier            |
| `npm run format:check`  | Fail if anything is unformatted |

## Frontend setup

From the repository root:

```bash
npm install
cp .env.example .env
npm run dev
```

Vite serves the app on `http://localhost:5173`.

### Environment variables

The API base URL is read from the environment - it is never hard-coded in the
client. Set it in `.env`:

```
VITE_API_URL=http://localhost:3000
```

Only variables prefixed with `VITE_` are exposed to the browser. If
`VITE_API_URL` is unset, `src/lib/api.ts` falls back to
`http://localhost:3000`. `.env` is gitignored; `.env.example` is the tracked
template.

### Frontend scripts

| Command                | Description                                     |
| ---------------------- | ----------------------------------------------- |
| `npm run dev`          | Start the Vite dev server                       |
| `npm run build`        | Type-check and build for production             |
| `npm run preview`      | Serve the production build                      |
| `npm run typecheck`    | Type-check app, Node and Cypress projects       |
| `npm run lint`         | Run ESLint                                      |
| `npm run format`       | Format with Prettier                            |
| `npm run format:check` | Fail if anything is unformatted                 |
| `npm test`             | Run the Vitest suite once                       |
| `npm run test:watch`   | Run Vitest in watch mode                        |
| `npm run cypress:open` | Open the Cypress runner                         |
| `npm run test:e2e`     | Start the dev server and run Cypress headlessly |

Backend scripts are also reachable from the root as `npm run backend:dev`,
`backend:seed`, `backend:test`, `backend:lint`, `backend:typecheck`,
`backend:format` and `backend:format:check`.

## Running the full stack

Use two terminals:

```bash
# terminal 1
npm run backend:dev

# terminal 2
npm run dev
```

Then sign in at `http://localhost:5173/login` with a seeded account. Every
seeded account uses the password `Password123!`:

| Role     | Email                        |
| -------- | ---------------------------- |
| Admin    | `alice.thompson@company.com` |
| Manager  | `bob.mitchell@company.com`   |
| Manager  | `carol.reeves@company.com`   |
| Employee | `david.jones@company.com`    |

The seed also creates `eve.knowles`, `frank.harrison` and `grace.williams` as
employees, each with a spread of requests across the leave year.

## Design system

Colour and type tokens live in the `@theme` block of `src/index.css`, and the
shared loading, empty and error components live in `src/components/states`.
Both are catalogued in [`docs/tokens.md`](docs/tokens.md) - read that before
building a new screen so states stay consistent rather than being reinvented
per feature.

## Testing

### Unit and component tests

Vitest runs in a jsdom environment with React Testing Library and
`@testing-library/jest-dom` matchers, configured in `vite.config.ts` and
`src/test/setup.ts`.

```bash
npm test
```

### End-to-end tests

Cypress specs live in `cypress/e2e` and run against
`http://localhost:5173`.

```bash
npm run cypress:open   # interactive
npm run test:e2e       # headless, boots the dev server itself
```

## Code style

ESLint (flat config, `eslint.config.js`) covers correctness; Prettier owns
formatting. `eslint-config-prettier` is applied last so the two never disagree.
The frontend and backend each have their own `.prettierrc.json` - the backend
keeps semicolons and double quotes, the frontend does not.

## Continuous integration

`.github/workflows/ci.yml` runs type-check, lint and tests for the frontend and
backend on every push and pull request against `main`.
