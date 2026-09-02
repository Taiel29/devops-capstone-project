# DevOps Capstone Template

![Build Status](https://github.com/Taiel29/devops-capstone-project/actions/workflows/ci-build.yaml/badge.svg)

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Python 3.9](https://img.shields.io/badge/Python-3.9-green.svg)](https://shields.io/)


Final project of IBM's **DevOps and Software Engineering Professional Certificate**. It's a RESTful microservice for managing customer accounts, built end to end across a full DevOps lifecycle: Agile planning, test driven development, continuous integration, security hardening, containerization, and automated continuous deployment to Kubernetes.

## What this project demonstrates

- **Agile planning**: user stories and sprint backlog managed on a Kanban board across three sprints.
- **Test Driven Development**: every route was written test-first with `nose`, keeping coverage at or above 95%.
- **Continuous Integration**: a GitHub Actions workflow runs the full test suite and lint checks on every push and pull request.
- **Security**: `Flask-Talisman` enforces HTTPS and security headers; `Flask-CORS` manages cross-origin policy.
- **Containerization**: the service is packaged with a multi-stage `Dockerfile`.
- **Continuous Deployment**: a Tekton pipeline builds, tests, and deploys the container to a Kubernetes/OpenShift cluster automatically on each commit, with no manual steps.

## Agile process
 
Before writing any code, the work was broken into three sprints. Each feature started as a user story on a Kanban board (GitHub Issues), written from the customer's perspective and moved through Backlog → In Progress → Review → Done as work progressed. The story for each route was assigned to me, then implemented test-first to satisfy that story's acceptance criteria. This kept scope for each sprint explicit and made it possible to track exactly which endpoint or pipeline stage was in flight at any point.

## Pipeline flow

```
push / pull request
      │
      ▼
GitHub Actions ── lint + unit tests (nose, ≥95% coverage)
      │
      ▼
Tekton pipeline ── clone → lint → test → build image → deploy to Kubernetes
      │
      ▼
Running service on Kubernetes/OpenShift
```

## API

The `Account` model:

| Field | Type | Optional |
|---|---|---|
| id | Integer | No |
| name | String(64) | No |
| email | String(64) | No |
| address | String(256) | No |
| phone_number | String(32) | Yes |
| date_joined | Date | No |

Endpoints implemented: `CREATE`, `READ`, `UPDATE`, `DELETE`, `LIST`, following REST conventions with proper status codes and error handlers.

## Project layout

```
├── service          <- microservice package
│   ├── common/       <- shared logging and error handlers
│   ├── config.py     <- Flask configuration
│   ├── models.py     <- persistence layer
│   └── routes.py     <- REST API routes
├── tekton/           <- CD pipeline definitions
├── tests/            <- unit tests and test factories
├── Dockerfile
└── requirements.txt
```

## Running it locally

```bash
# create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate

# install dependencies
make install

# start Postgres in Docker
make db

# run the test suite
nosetests

# run the service
flask run
```

## Why these choices

- **TDD over write-then-test**: writing the test first forces the API contract to be defined before the implementation, which cut down on rework once the routes in `routes.py` were wired to the persistence layer.
- **Flask-Talisman**: rather than hand-rolling security headers, using a maintained library reduces the chance of missing a header (HSTS, CSP, etc.) that a manual implementation could overlook.
- **Tekton over a single GitHub Actions deploy step**: Tekton runs natively inside the Kubernetes cluster, so the deployment pipeline itself is versioned and portable across clusters instead of being tied to GitHub's runners.

## Acknowledgment

Built on the starter template provided by IBM / Coursera as part of the [DevOps and Software Engineering Professional Certificate](https://www.coursera.org/professional-certificates/devops-and-software-engineering).

My own work in this repository:
- `service/routes.py` — the REST API routes
- `tests/` — the test suite, except `test_cli_commands.py`
- `tekton/` — the CD pipeline definitions
- `deploy/` — deployment configuration
- `.github/workflows/` and issue templates
- `setup.cfg`, `Dockerfile`

## License

Licensed under the Apache License 2.0. See [LICENSE](LICENSE).
