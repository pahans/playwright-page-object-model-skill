# Full Example: Registration Page

Based on a real Playwright suite testing a course registration form. The spec
originally called `page.getByRole(...)` directly for most fields — only
`firstName` was wrapped in the page object. This is what it looks like with
every field wrapped.

## `pages/registrationPage.ts`

```ts
import { expect, Locator, Page } from "@playwright/test";

type RegistrationDetails = {
  firstName: string;
  lastName: string;
  email: string;
  course: string;
  month: string;
  referralSource: string;
};

export class RegistrationPage {
  private readonly firstNameInput: Locator;
  private readonly lastNameInput: Locator;
  private readonly emailInput: Locator;
  private readonly courseSelect: Locator;
  private readonly monthSelect: Locator;
  private readonly registerButton: Locator;
  private readonly processingButton: Locator;
  private readonly successHeading: Locator;

  constructor(private readonly page: Page) {
    this.firstNameInput = this.page.getByRole("textbox", {
      name: "First Name *",
    });
    this.lastNameInput = this.page.getByRole("textbox", {
      name: "Last Name *",
    });
    this.emailInput = this.page.getByRole("textbox", { name: "Email *" });
    this.courseSelect = this.page.getByLabel(
      "Select the Course you would",
    );
    this.monthSelect = this.page.getByLabel(
      "Select the Month of the Batch",
    );
    this.registerButton = this.page.getByRole("button", {
      name: "Register",
    });
    this.processingButton = this.page.getByRole("button", {
      name: "Processing",
    });
    this.successHeading = this.page.getByRole("heading", { name: "Success" });
  }

  async goto() {
    await this.page.goto("/registration-form/");
  }

  async expectToBeOnRegistration() {
    await expect(this.page).toHaveURL("/registration-form/");
  }

  async fillFirstName(firstName: string) {
    await this.firstNameInput.fill(firstName);
  }

  async fillLastName(lastName: string) {
    await this.lastNameInput.fill(lastName);
  }

  async fillEmail(email: string) {
    await this.emailInput.fill(email);
  }

  async selectCourse(course: string) {
    await this.courseSelect.selectOption(course);
  }

  async selectMonth(month: string) {
    await this.monthSelect.selectOption(month);
  }

  async selectReferralSource(source: string) {
    await this.page.getByRole("radio", { name: source }).check({ force: true });
  }

  async submit() {
    await this.registerButton.click();
  }

  async expectProcessing() {
    await expect(this.processingButton).toBeVisible();
  }

  async expectSuccess() {
    await expect(this.successHeading).toBeVisible();
  }

  // Composite action: keeps field-by-field methods for targeted tests
  // (e.g. validation) while giving the common path a single call.
  async register(details: RegistrationDetails) {
    await this.fillFirstName(details.firstName);
    await this.fillLastName(details.lastName);
    await this.fillEmail(details.email);
    await this.selectCourse(details.course);
    await this.selectMonth(details.month);
    await this.selectReferralSource(details.referralSource);
    await this.submit();
  }
}
```

## `fixtures.ts`

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

## `tests/registration.spec.ts`

```ts
import { test, expect } from "../fixtures";

test.describe("Registration Feature", () => {
  test.beforeEach(async ({ registrationPage }) => {
    await registrationPage.goto();
  });

  test("Successful registration", async ({ registrationPage }) => {
    await registrationPage.expectToBeOnRegistration();

    await registrationPage.register({
      firstName: "Pahan",
      lastName: "Sara",
      email: "pahan123@gmail.com",
      course: "API Automation",
      month: "May",
      referralSource: "Kijiji",
    });

    await registrationPage.expectProcessing();
    await registrationPage.expectSuccess();
  });
});
```

Note what's gone from the spec: no `page.getByRole` calls, no `expect()`
calls, no knowledge of how the form is built — just page-object method calls
describing user behavior. That's the signal a page object is complete.
