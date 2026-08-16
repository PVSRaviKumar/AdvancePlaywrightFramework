# Advanced Playwright Framework

A comprehensive, enterprise-grade test automation framework built with Playwright, featuring advanced patterns, fixtures, utilities, and configuration management for scalable testing solutions.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Project Structure Details](#project-structure-details)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

The Advanced Playwright Framework is designed to provide a robust, scalable, and maintainable test automation solution. It incorporates industry best practices, including Page Object Model (POM), custom fixtures, API clients, test data management, and comprehensive logging.

## ✨ Features

- **Page Object Model (POM)**: Organized page classes for maintainable test code
- **Custom Fixtures**: Pre-built Playwright fixtures for common testing scenarios
- **API Client Integration**: Dedicated API client structure for API testing
- **Test Data Management**: Centralized test data storage with support for multiple formats
- **Environment Configuration**: Environment-specific configurations using `.env` files
- **Logging & Reporting**: Winston-based logging with Allure reporting support
- **Data Generation**: Faker.js integration for dynamic test data generation
- **Data Validation**: AJV with format validation for JSON schema validation
- **Excel/CSV Support**: csv-parse and xlsx for test data file handling
- **JSON Path Queries**: jsonpath-plus for complex JSON data extraction

## 📁 Project Structure

```
AdvancePlaywrightFramework/
├── src/
│   ├── api/                 # API client implementations
│   ├── config/              # Environment & configuration files
│   ├── fixtures/            # Custom Playwright fixtures
│   ├── pages/               # Page Object Model classes
│   ├── testdata/            # Test data files (JSON, CSV, XLSX)
│   ├── tests/               # Test case implementations
│   └── utils/               # Utility functions & helpers
├── docs/                    # Documentation
├── rules/                   # Testing rules & standards
├── .github/                 # GitHub workflows & actions
├── .env                     # Environment variables
├── package.json             # Project dependencies
├── playwright.config.ts     # Playwright configuration
├── tsconfig.json            # TypeScript configuration
└── README.md               # This file

```

## 📦 Prerequisites

- **Node.js**: Version 14.x or higher
- **npm**: Version 6.x or higher
- **Git**: For version control

## 🚀 Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/PVSRaviKumar/AdvancePlaywrightFramework.git
   cd AdvancePlaywrightFramework
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Configure environment variables**:
   ```bash
   cp .env.example .env
   ```
   Edit `.env` file with your specific configuration:
   ```
   BASE_URL=https://example.com
   ENVIRONMENT=staging
   LOG_LEVEL=info
   ```

## ⚙️ Configuration

### Playwright Configuration
Edit `playwright.config.ts` to customize:
- Browser settings
- Test timeouts
- Retry configurations
- Report generation

### Environment Variables
Configure in `.env` file:
```env
BASE_URL=https://your-app-url.com
ENVIRONMENT=staging
HEADLESS=true
LOG_LEVEL=info
API_BASE_URL=https://api.example.com
```

## 🧪 Usage

### Running Tests

```bash
# Run all tests
npm test

# Run tests in specific browser
npm test -- --project=chromium

# Run tests with UI mode
npm test -- --ui

# Run specific test file
npm test tests/example.spec.ts

# Run tests with specific tag
npm test -- --grep @smoke
```

### Debugging Tests

```bash
# Run with Playwright Inspector
npm test -- --debug

# Generate trace files
npm test -- --trace on
```

### Generating Reports

```bash
# Generate Allure report
npm run allure:report

# Open Allure report
npm run allure:open
```

## 📂 Project Structure Details

### `/src/api`
Contains API client implementations and endpoints for API testing.
```
- baseClient.ts       # Base API client with common methods
- endpoints.ts        # API endpoint definitions
- services/           # Service-specific API clients
```

### `/src/config`
Environment and configuration files.
```
- config.ts          # Main configuration handler
- browsers.ts        # Browser-specific configurations
- environments/      # Environment-specific settings
```

### `/src/fixtures`
Custom Playwright fixtures extending base functionality.
```
- commonFixtures.ts  # Common fixtures (auth, page setup)
- apiFixtures.ts     # API testing fixtures
- dataFixtures.ts    # Test data fixtures
```

### `/src/pages`
Page Object Model (POM) implementations.
```
- basePage.ts        # Base page class with common methods
- loginPage.ts       # Login page object
- dashboardPage.ts   # Dashboard page object
```

### `/src/testdata`
Test data files and data loaders.
```
- users.json         # User credentials
- testData.csv       # CSV test data
- testData.xlsx      # Excel test data
```

### `/src/tests`
Test specifications organized by feature/module.
```
- auth/              # Authentication tests
- dashboard/         # Dashboard tests
- integration/       # Integration tests
```

### `/src/utils`
Utility functions and helpers.
```
- logger.ts          # Winston logger configuration
- helpers.ts         # Common helper functions
- validators.ts      # Data validation utilities
```

## 🛠️ Technologies & Dependencies

| Package | Purpose |
|---------|---------|
| `@playwright/test` | Web automation framework |
| `typescript` | Type safety |
| `dotenv` | Environment variable management |
| `winston` | Logging |
| `@faker-js/faker` | Test data generation |
| `csv-parse` | CSV parsing |
| `xlsx` | Excel file handling |
| `jsonpath-plus` | JSON data extraction |
| `ajv` & `ajv-formats` | JSON schema validation |
| `allure-playwright` | Test reporting |

## 📝 Writing Tests

### Basic Test Example
```typescript
import { test, expect } from '@playwright/test';
import { LoginPage } from '../pages/loginPage';

test.describe('Authentication', () => {
  test('should login successfully', async ({ page }) => {
    const loginPage = new LoginPage(page);
    await loginPage.navigate();
    await loginPage.login('user@example.com', 'password');
    await expect(page).toHaveURL('/dashboard');
  });
});
```

## 📊 Reporting

Tests generate reports in multiple formats:
- **HTML Report**: View with `npm run show:report`
- **Allure Report**: Detailed insights with `npm run allure:open`
- **Console Output**: Real-time test execution logs
- **Log Files**: Stored in `logs/` directory

## 🔒 Best Practices

1. **Use Page Objects**: Encapsulate page interactions in page classes
2. **Avoid Hard Waits**: Use Playwright's built-in waiting mechanisms
3. **Organize Tests**: Group related tests using `test.describe()`
4. **Data Management**: Use testdata folder for external data
5. **Error Handling**: Implement proper error logging and reporting
6. **CI/CD Integration**: Use GitHub Actions for automated testing

## 🤝 Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Commit changes: `git commit -am 'Add new feature'`
3. Push to branch: `git push origin feature/your-feature`
4. Submit a Pull Request

## 📞 Support

For issues, questions, or suggestions, please create an issue in the [GitHub repository](https://github.com/PVSRaviKumar/AdvancePlaywrightFramework/issues).

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

**Happy Testing! 🎭**

For more information about Playwright, visit [playwright.dev](https://playwright.dev)
