# Advanced Playwright Framework

An environment-aware Playwright test automation framework built with TypeScript, Page Object Model classes, reusable utilities, and custom reporting.

## Prerequisites

- Node.js 18 or newer
- npm
- Git

## Installation

```bash
git clone https://github.com/PVSRaviKumar/AdvancePlaywrightFramework.git
cd AdvancePlaywrightFramework
npm install
npx playwright install chromium
```

## Configuration

Create a `.env` file in the project root. It is ignored by git, so keep credentials there rather than in source control.

```env
TTA_ENV=qa
BASE_URL=https://app.thetestingacademy.com/
QA_BASE_URL=https://app.thetestingacademy.com/
STG_BASE_URL=https://stage.thetestingacademy.com/
DEV_BASE_URL=http://localhost:3000/
API_BASE_URL=https://restful-booker.herokuapp.com/
LOG_LEVEL=info
ATTACH_SCREENSHOTS=false
USERNAME=your-username
PASSWORD=your-password
```

`BASE_URL` takes precedence. When it is not set, `TTA_ENV` selects the URL:

| `TTA_ENV` | URL variable | Default URL |
| --- | --- | --- |
| `api` | `API_BASE_URL` | `https://restful-booker.herokuapp.com` |
| `dev`, `local` | `DEV_BASE_URL` | `http://localhost:3000` |
| `qa` | `QA_BASE_URL` | `https://app.thetestingacademy.com` |
| `stage`, `stg`, `staging` | `STG_BASE_URL` | `https://stage.thetestingacademy.com` |
| `prod`, `production` | `PROD_BASE_URL` | `https://app.thetestingacademy.com` |

The default environment is `qa`. `BASE_URL` takes precedence over `TTA_ENV` and the environment-specific URL variables. Set `ATTACH_SCREENSHOTS=true` to attach screenshots on test failure; videos and traces are enabled for every test run.

## Running Tests

Tests are run with the Playwright CLI because no npm scripts are currently defined.

```bash
# Run all tests
npx playwright test

# Run the login tests
npx playwright test src/tests/Login/login.spec.ts

# List discovered tests without running them
npx playwright test --list

# Run Chromium explicitly
npx playwright test --project=chromium

# Run tests in UI mode
npx playwright test --ui

# Run with the Playwright Inspector
npx playwright test --debug
```

The configuration in `playwright.config.ts` uses a 60-second test timeout, a 10-second assertion timeout, fully parallel execution, and Chromium as the browser project. Videos and traces are collected for every test; screenshots are collected only when `ATTACH_SCREENSHOTS=true`.

### Shared fixtures

The custom test fixture in `src/fixtures/test-base.ts` provides ready-to-use page objects and reusable application states:

```ts
import { test, expect } from '@fixtures/test-base';

test('add an item to the cart', async ({ loginWithSelectedItem, cartPage }) => {
  await cartPage.open();
  expect(await cartPage.rowCount()).toBe(1);
});
```

Use the fixture when a test needs shared page-object setup or an authenticated state. Tests that do not need those helpers can continue importing `test` directly from `@playwright/test`.

## Reports and Artifacts

The configured reporters produce the Playwright HTML report, terminal output, and a custom report from `src/utils/CustomReporter.ts`.

```bash
npx playwright show-report
```

Generated artifacts are written to `test-results/`, `playwright-report/`, `reports/`, and `tta-report/`.

## Project Structure

```text
src/
  api/       API client implementations
  config/    Environment and configuration modules
  fixtures/  Reusable Playwright fixtures and application states
  pages/     Page Object Model classes
  testdata/  Test data files
  tests/     Playwright test specifications
  utils/     Logging, reporting, and test-data helpers
tests/       Example Playwright tests
docs/        Project documentation
rules/       Testing rules and standards
playwright.config.ts
tsconfig.json
```

## Guidelines

- Keep browser interactions in page objects.
- Prefer Playwright's built-in waiting and locator assertions over hard waits.
- Keep test data in `src/testdata/` and environment-specific values in `.env`.
- Use `test.describe()` to group related scenarios.
- Review failure traces, videos, and screenshots when diagnosing a test.

## Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Run the relevant Playwright tests.
3. Commit your changes and push the branch.
4. Open a pull request.

For issues or suggestions, use the [GitHub repository](https://github.com/PVSRaviKumar/AdvancePlaywrightFramework/issues).
