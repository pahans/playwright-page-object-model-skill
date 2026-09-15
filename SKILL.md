---
name: playwright-page-object-model
description: Use when writing or reviewing Playwright TypeScript E2E tests — structuring page objects, locators, and custom fixtures, or when a spec file has raw page.getByRole/page.locator calls that belong in a page object instead.
---

# Playwright Page Object Model

## Overview

Each page/screen is a class holding its locators and the actions/assertions a
test can perform on it. Tests never touch `page.getBy*` directly — they call
page object methods. Page objects are wired into tests via a custom fixture,
not instantiated manually in each test.

## When to Use

- Starting a new Playwright + TypeScript test suite.
- A `.spec.ts` file has raw locator calls (`page.getByRole(...)`,
  `page.locator(...)`) instead of calling a page object method — that's the
  signal this pattern is being skipped.
- Adding a new page/flow to a suite that already follows this pattern.

Skip it for a single throwaway script or exploratory Playwright codegen run —
POM overhead isn't worth it until there's a real suite.

## Core Pattern

**File layout:**

```
pages/         # one class per page or component
  registrationPage.ts
tests/         # specs, one describe block per feature
  registration.spec.ts
fixtures.ts    # extends base `test` with page-object fixtures
```

**Page object rules:**

- One class per page. Constructor takes `private readonly page: Page`.
- Every locator is a `private` field, built once in the constructor — never
  re-queried inline inside methods, never exposed to the test.
- Locators use semantic queries (`getByRole`, `getByLabel`, `getByText`) over
  CSS/XPath selectors — they read like what a user sees, not how the DOM is
  built.
- Methods are named for the user action or assertion (`fillFirstName`,
  `expectToBeOnRegistration`), not the underlying Playwright call. Assertion
  methods (`expect...`) live on the page object too, so tests read as
  behavior, not as a list of `expect()` calls.
- No conditional/branching logic in page objects — if a flow needs
  branching, that belongs in the test or a helper, not the page class.

**Fixture wiring (`fixtures.ts`):** extend Playwright's base `test` so every
spec gets a ready-to-use page object instead of `new RegistrationPage(page)`
in every test:

```ts
import { test as baseTest, expect } from "@playwright/test";
import { RegistrationPage } from "./pages/registrationPage";

type Pages = {
  registrationPage: RegistrationPage;
};

const test = baseTest.extend<Pages>({
  registrationPage: async ({ page }, use) => {
    await use(new RegistrationPage(page));
  },
});

export { test, expect };
```

Specs then `import { test, expect } from "../fixtures"` — **never** from
`@playwright/test` directly, or the fixture is bypassed.

## Quick Reference

| Convention | Rule |
|---|---|
| Locators | `private` fields, built in constructor, semantic queries only |
| Page → test coupling | Test calls methods; never reaches into `page` for app-specific elements |
| New fixture | Add a field to `Pages` type + a factory in `fixtures.ts`, one per page |
| New field on a form | Add locator + a `fillX`/`selectX` method to the page object, not inline in the spec |
| Assertions about the page | Live on the page object (`expectToBeOnRegistration`, `expectSuccess`) |
| Multi-field actions | Add a composite method (`register(data)`) instead of chaining field methods in every test |

## Full Example

A complete registration page object, its fixture wiring, and a spec that
never touches a raw locator — see [example.md](example.md).

## Common Mistakes

- **Locators or `expect()` calls inline in the spec.** If a test does
  `page.getByRole(...)`, that locator belongs in the page object, and the
  action/assertion belongs in a method.
- **CSS/XPath selectors** (`page.locator(".btn-primary")`) instead of
  `getByRole`/`getByLabel`/`getByText` — brittle and hides intent.
- **`new PageObject(page)` inside a test** instead of adding it to
  `fixtures.ts` — bypasses the shared fixture and diverges page objects
  across specs.
- **Locators recomputed per call** instead of built once in the constructor.
- **Partial page objects** — a class that wraps 2 of a form's 8 fields while
  the rest live as raw `page.getByRole` calls in the spec. Wrap every field
  the tests interact with, not just the first one added.
