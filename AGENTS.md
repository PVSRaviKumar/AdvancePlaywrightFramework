---
name: agents
description: Instructions for AI agents working on the Advanced Playwright Framework test automation project
---

# Advanced Playwright Framework — Agent Instructions

This is an enterprise-grade test automation framework built with Playwright, featuring Page Object Model (POM), custom fixtures, API testing, and multi-environment support.

## Quick Facts

- **Test Runner**: Playwright v1.62.1 (`@playwright/test`)
- **Language**: TypeScript (configured but no tsconfig.json yet — reference Playwright docs)
- **Configuration**: Environment-driven via `.env` files and `playwright.config.ts`
- **Pattern**: Page Object Model (POM) with custom fixtures
- **Reporting**: HTML (default) + Allure + custom reporter (`src/utils/CustomReporter.ts`)
- **Key Dependencies**: Faker.js (data), AJV (validation), Winston (logging), CSV/XLSX (test data)
- **Main Entry**: `playwright.config.ts` — defines test directory, timeouts, reporters, and environment resolution

## Project Structure

| Folder | Purpose | Notes |
|--------|---------|-------|
| `src/tests/` | Test specifications | Organized by feature/module (e.g., `auth/`, `dashboard/`) |
| `src/pages/` | Page Object Model classes | Base class pattern: `basePage.ts` with common methods |
| `src/fixtures/` | Custom Playwright fixtures | Extend @playwright/test fixtures (auth, API, data) |
| `src/api/` | API client implementations | REST API testing, endpoints, services |
| `src/config/` | Configuration & environment | Loaded from `.env`; environment-specific URLs |
| `src/utils/` | Utility functions & helpers | Logging, data loading, custom reporter |
| `src/testdata/` | Test data files | JSON, CSV, XLSX; loaded via utilities |
| `tests/example.spec.ts` | Sample test file | Basic Playwright pattern (can be refactored into POM) |

## Before Starting

### Environment Setup
1. Tests use **TTA_ENV** to resolve base URLs (values: `api`, `dev`, `local`, `qa`, `stage`, `prod`)
2. Default is `qa` → `https://app.thetestingacademy.com`
3. Credentials are in `.env` (USERNAME, PASSWORD, etc.)
4. Modify `.env` for local development (e.g., `TTA_ENV=local` + `DEV_BASE_URL=http://localhost:3000`)

### Key Configuration
- **Test timeout**: 60 seconds (global), 10 seconds (assertions)
- **Parallel execution**: Enabled by default (`fullyParallel: true`)
- **Screenshots/Video/Trace**: Captured on failure
- **CI retries**: 2 retries when running in CI; 0 locally

## Common Tasks

### Running Tests

```bash
# Install dependencies (if needed)
npm install

# Run all tests
npx playwright test

# Run specific test file
npx playwright test src/tests/auth/login.spec.ts

# Run tests by tag (e.g., @smoke)
npx playwright test --grep @smoke

# UI mode (interactive)
npx playwright test --ui

# Debug mode (Playwright Inspector)
npx playwright test --debug

# Generate HTML report
npx playwright show-report
```

### Writing a Test

1. **Create a page object** in `src/pages/` (e.g., `loginPage.ts`):
   ```typescript
   import { Page } from '@playwright/test';
   
   export class LoginPage {
     constructor(private page: Page) {}
   
     async goto() {
       await this.page.goto('/login');
     }
   
     async login(username: string, password: string) {
       await this.page.fill('[name="username"]', username);
       await this.page.fill('[name="password"]', password);
       await this.page.click('button[type="submit"]');
     }
   }
   ```

2. **Create a test file** in `src/tests/` (e.g., `src/tests/auth/login.spec.ts`):
   ```typescript
   import { test, expect } from '@playwright/test';
   import { LoginPage } from '../../pages/loginPage';
   
   test.describe('Login Tests @auth', () => {
     let loginPage: LoginPage;
   
     test.beforeEach(async ({ page }) => {
       loginPage = new LoginPage(page);
       await loginPage.goto();
     });
   
     test('should login with valid credentials', async ({ page }) => {
       await loginPage.login(process.env.USERNAME!, process.env.PASSWORD!);
       await expect(page).toHaveURL(/dashboard/);
     });
   });
   ```

### Using Fixtures

Create custom fixtures in `src/fixtures/` and import in tests:
```typescript
import { test as base } from '@playwright/test';

export const test = base.extend({
  authenticatedPage: async ({ page }, use) => {
    // Setup: login before each test
    await page.goto('/login');
    await page.fill('[name="username"]', process.env.USERNAME!);
    await page.fill('[name="password"]', process.env.PASSWORD!);
    await page.click('button[type="submit"]');
    
    await use(page);
    // Teardown: logout after test
  },
});
```

### API Testing

1. Create a base client in `src/api/baseClient.ts`:
   ```typescript
   import axios, { AxiosInstance } from 'axios';
   
   export class BaseClient {
     protected client: AxiosInstance;
   
     constructor(baseURL: string) {
       this.client = axios.create({ baseURL });
     }
   }
   ```

2. Create service clients (e.g., `src/api/bookingService.ts`):
   ```typescript
   export class BookingService extends BaseClient {
     async getBookings() {
       return this.client.get('/bookings');
     }
   }
   ```

3. Use in tests:
   ```typescript
   const bookingService = new BookingService(process.env.API_BASE_URL!);
   const bookings = await bookingService.getBookings();
   ```

### Loading Test Data

Use data files from `src/testdata/`:
```typescript
import fs from 'fs';

const users = JSON.parse(fs.readFileSync('./src/testdata/users.json', 'utf-8'));
const csvData = parse(fs.readFileSync('./src/testdata/data.csv', 'utf-8'));
```

Or create a utility loader in `src/utils/`:
```typescript
export function loadTestData(filename: string) {
  return JSON.parse(fs.readFileSync(`./src/testdata/${filename}`, 'utf-8'));
}
```

### Logging

Winston is installed for structured logging. Create a logger utility:
```typescript
import winston from 'winston';

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'logs/test.log' }),
  ],
});
```

### Validation

AJV is available for JSON schema validation:
```typescript
import Ajv from 'ajv';

const ajv = new Ajv();
const validate = ajv.compile(mySchema);
const valid = validate(data);
```

## Known Issues & Workarounds

| Issue | Solution |
|-------|----------|
| **No npm scripts defined** | Add scripts to `package.json` (e.g., `"test": "playwright test"`, `"test:ui": "playwright test --ui"`) |
| **Empty fixture/page/api folders** | These are scaffolded; follow "Writing a Test" above to populate |
| **tsconfig.json missing** | Not strictly required for `npx playwright test`; create if using additional TypeScript build steps |
| **CustomReporter.ts referenced but missing** | Implement in `src/utils/CustomReporter.ts` extending Playwright's Reporter interface |

## When Adding New Tests

1. **Organize by feature**: Create folders in `src/tests/` (e.g., `src/tests/checkout/`, `src/tests/inventory/`)
2. **Use POM**: Extract page objects into `src/pages/` for reusability
3. **Leverage fixtures**: Common setup (auth, API client, data) should be in `src/fixtures/`
4. **Tag tests**: Use `@smoke`, `@regression`, `@wip` for test categorization
5. **Environment-aware**: Use `process.env.BASE_URL` and `process.env.TTA_ENV` for multi-environment support
6. **Follow naming**: Test files: `*.spec.ts`; Page objects: `*Page.ts`; Fixtures: `*Fixtures.ts`

## Environment Files

| File | Purpose |
|------|---------|
| `.env` | Local override; not tracked by git |
| `.env.example` | Template for CI/CD and team setup |

## Documentation & Links

- [Playwright Docs](https://playwright.dev)
- [Allure Reporting](https://docs.qameta.io/allure/)
- [Faker.js](https://fakerjs.dev)
- [Page Object Model Pattern](https://playwright.dev/docs/pom) — refer to official docs for best practices
- `README.md` — features and setup overview
- `playwright.config.ts` — test configuration and environment resolution logic

## Tips for AI Agents

- **Always check `.env`** before running tests; verify `TTA_ENV` and `BASE_URL`
- **Use `playwright test --ui`** for exploratory debugging
- **Refer to `playwright.config.ts`** for test timeout, retry, and reporter settings
- **Leverage `baseURL`** in config — page objects can use relative URLs
- **Generate reports**: Run `npx playwright show-report` after test execution
- **Check browser/device defaults**: Currently configured for Chromium Desktop; add other projects in `playwright.config.ts` if needed
