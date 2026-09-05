# Advance Playwright Framework 2x

A TypeScript test automation framework built on [Playwright](https://playwright.dev/) for UI and API testing, with a modular structure for page objects, fixtures, config, and test data.

## Tech Stack

- **Playwright** (`@playwright/test`) - browser automation and test runner
- **TypeScript** - strict mode enabled
- **Faker.js** - test data generation
- **Ajv / ajv-formats** - JSON schema validation
- **jsonpath-plus** - JSON response querying
- **csv-parse / xlsx** - data-driven testing from CSV/Excel sources
- **Winston** - logging
- **Allure Playwright** - test reporting
- **Custom TTA Reporter** - real-time HTML report with screenshots, videos, traces, and AI-powered RCA analysis
- **dotenv** - environment configuration

## Project Structure

```
.
├── .github/workflows/     # CI pipeline (GitHub Actions)
├── docs/                  # Documentation
├── rules/                 # Project/test rules and conventions
├── src/
│   ├── ai/                # AI agents (RCA, Flaky Analyzer)
│   ├── api/               # API test specs and request helpers
│   ├── config/            # Environment and framework configuration
│   ├── fixtures/          # Custom Playwright fixtures
│   ├── pages/             # Page Object Model classes
│   ├── testdata/          # Static and generated test data
│   ├── tests/             # Test specs
│   └── utils/             # Shared utility functions and Custom Reporter
├── playwright.config.ts   # Playwright configuration
├── tsconfig.json          # TypeScript configuration and path aliases
└── package.json
```

## Prerequisites

- Node.js (LTS recommended)
- npm

## Setup

Install dependencies and Playwright browsers:

```bash
npm install
npx playwright install
```

Create a `.env` file in the project root to override defaults (see [Environment Configuration](#environment-configuration)):

```bash
TTA_ENV=qa
BASE_URL=
QA_BASE_URL=https://app.thetestingacademy.com
STG_BASE_URL=https://stage.thetestingacademy.com
DEV_BASE_URL=http://localhost:3000
PROD_BASE_URL=https://app.thetestingacademy.com
API_BASE_URL=https://restful-booker.herokuapp.com
```

## Environment Configuration

The base URL is resolved in `playwright.config.ts` based on the `TTA_ENV` environment variable:

| `TTA_ENV` value          | Resolves to                                  |
|---------------------------|-----------------------------------------------|
| `qa` (default)            | `QA_BASE_URL` or `https://app.thetestingacademy.com` |
| `dev` / `local`           | `DEV_BASE_URL` or `http://localhost:3000`     |
| `stg` / `stage` / `staging` | `STG_BASE_URL` or `https://stage.thetestingacademy.com` |
| `prod` / `production`     | `PROD_BASE_URL` or `https://app.thetestingacademy.com` |
| `api`                     | `API_BASE_URL` or `https://restful-booker.herokuapp.com` |

`BASE_URL`, if set, always takes precedence over the above.

## Path Aliases

TypeScript path aliases are configured in `tsconfig.json` for cleaner imports:

| Alias         | Maps to           |
|---------------|--------------------|
| `@api/*`      | `src/api/*`        |
| `@config/*`   | `src/config/*`     |
| `@fixtures/*` | `src/fixtures/*`   |
| `@pages/*`    | `src/pages/*`      |
| `@testdata/*` | `src/testdata/*`   |
| `@utils/*`    | `src/utils/*`      |

## Running Tests

Run the full suite:

```bash
npx playwright test
```

Run only the API tests:

```bash
npx playwright test --project=api
```

Run only the browser (UI) tests:

```bash
npx playwright test --project=chromium
```

Run a specific test file:

```bash
npx playwright test src/tests/example.spec.ts
```

Run in headed mode:

```bash
npx playwright test --headed
```

Run against a specific environment:

```bash
TTA_ENV=stage npx playwright test
```

View the HTML report:

```bash
npx playwright show-report
```

View the TTA custom report (auto-generated):

```bash
open tta-report/index.html
```

## Custom TTA Reporter

The framework includes a custom Playwright reporter (`src/utils/CustomReporter.ts`) that generates a real-time HTML report in the `tta-report/` directory. Features include:

- **Real-time updates** - report refreshes during test execution
- **Screenshots** - inline screenshots for each test
- **Videos** - embedded video playback with step-level timeline
- **Traces** - downloadable trace files for debugging
- **AI-powered RCA** - root cause analysis for failed tests (requires LLM API key)
- **Flaky test detection** - compares builds to identify flaky tests

## Test Configuration

Defined in `playwright.config.ts`:

- Test directories:
  - `chromium` project: `src/tests` (UI/browser tests)
  - `api` project: `src/api` (API tests)
- Timeout: 60s per test, 10s per assertion
- Fully parallel execution
- Retries: 2 on CI, 0 locally
- Screenshots: always captured
- Video: always recorded
- Trace: always captured
- Browser project: Chromium (Desktop Chrome)
- Custom Reporter: real-time TTA HTML report with screenshots, videos, traces, and AI-powered RCA/flaky analysis

## Continuous Integration

`.github/workflows/playwright.yml` runs on every push and pull request to `main`/`master`:

1. Checks out the repository
2. Sets up Node.js (LTS)
3. Installs dependencies (`npm ci`)
4. Installs Playwright browsers with OS dependencies
5. Runs the Playwright test suite
6. Uploads the HTML report as a build artifact (30-day retention)

## License

ISC