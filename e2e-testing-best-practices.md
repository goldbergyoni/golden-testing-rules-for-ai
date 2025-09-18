# End-To-End Testing Rules for AI

IMPORTANT: Apply these rules exclusively to system-wide end-to-end tests that span multiple processes. Do not apply them to integration, component or unit tests with mocked backends. System-wide E2E tests are complex, slow, and limited in quantity - therefore relaxed readability standards and less stringent best practices are acceptable

## Best Practices

A.5. The test name should summarize the test flow in a short sentence. It should also describe a stakeholder, an action, and some expectations. For example, 'The user can purchase an item, receive an email and then a supply request was created'

A.8. Important: Each test that is self-contained and never relies on other tests state or generated artifacts. Consequently, if a test depends on any state, it should create it itself or ensure it was created in a hook

A.11. Its test should start with a fresh dedciated set of data that is created in beforeEach or during the test. Minimize assumption on existing states

A.14. Test real user scenarios, simulating actual user behavior rather than testing technical implementation details

A.17. Use only user-facing locators based on ARIA roles, labels, or accessible names (e.g., page.getByRole('button', {name: 'Submut button'})) - avoid CSS selectors, test IDs, xpath, $, or any other implementation-specific locators

A.20. Use the framework mechanism for asserting safely on elements: If the framework can tell deterministically when the re-render ended (e.g., testing-library), just include standard non-awaitable assertions. In framework like Playwright that don't interact directly with the Renderer, use auto-retriable assertions (a.k.a web-first assertions) with await: `await expect(locator).toContainText('sone string');`

A.23. Only visit and test within the application boundaries, not third-party services or external APIs

A.26. Keep tests under 15 statements by encapsulating implementation details and multi-step operations in helper functions - each helper function should represent a meaningful part of the user's journey (e.g., 'loginAsAdmin', 'addProductToCart', 'completeCheckout') rather than exposing low-level browser interactions

A.35. Implement comprehensive test data management with automatic cleanup to prevent test interference and data pollution

A.40. Do not assume or rely on the page structure or layout. Avoid using positional selectors like nth(i), first(), last() and similar

A.44. Due to the trust in auto-retriable assertion, avoid time-based waiting like setTimeout or page.waitForTimeout(2000); Also avoid waiting for some internal element appearance (e.g., Playwright waitForSelector) as it couple the test to the implementation

A.48. Avoid approaching and asserting on external systems. Alternativelly, assert that the navigation happened and if/needed simulate a stubbed response

A.52. Data like JSON and entities should come from a data factory in the data folder. Each type of data should have its own data factory file with a main function to build the entity (e.g., buildOrder, buildUser). The factory function should return default data but also allow the caller to provide overrides to specific fields, this way each test can modify specific field values

<example>
`
import { faker } from "@faker-js/faker";
import { FileContext } from "../types";

export function buildFileFromIDE(overrides: Partial<FileContext> = {}): FileContext {
  return {
    path: faker.system.filePath(),
    type: faker.helpers.arrayElement(["file", "folder"]),
    ...overrides,
  };
}
`
</example>

A.55. For the test data, use meaningful domain data, not dummy values. When setting a common universal data in a field like dates, addresses or anything that is not domain-specific, use libraries that provide realistic real-world data like faker and alike

A.58. Avoid custom coding, loop and Array.prototype function, stick to built-in expect APIs, including for Arrays

A.62. Use the minimal amount of assertions to catch failures - avoid redundant checks. Use: `expect(response).toEqual([{id: '123'}, {id: '456'}])` instead of:

```
expect(response).not.toBeNull()       // redundant
expect(Array.isArray(response)).toBe(true)  // redundant
expect(response.length).toBe(2)       // redundant
expect(response[0].id).toBe('123')    // redundant
```

The single assertion will catch null, non-array, and wrong data issues

A.65. Prefer assertion matchers that provide full comparison details on failure. Use `expect(actualArray).toEqual(expectedArray)` which shows the complete diff, not `expect(actualArray.contains(expectedValue)).toBeTrue()` which only shows true/false


## Examples

### BAD E2E Test Example

```typescript
// BAD E2E TEST EXAMPLE - Violates multiple best practices
test('Should purchase item', async ({ page }) => { // 👎🏻 violates A.5 (vague title)
  await page.goto('/products') // 👎🏻 violates A.8, A.11 (assumes existing user session)

  await page.getByText('iPhone 15 Pro').click() // 👎🏻 violates A.8, A.11 (assumes specific product exists)
  await page.locator('#add-to-cart-btn').click() // 👎🏻 violates A.17 (CSS selector)
  await page.goto('/checkout')

  await page.waitForSelector('.form-loaded') // 👎🏻 violates A.44 (implementation detail)
  await page.locator('#email').fill('foo@test.com') // 👎🏻 violates A.17, A.52, A.55 (CSS + dummy data)
  await page.locator('button').last().click() // 👎🏻 violates A.17, A.40 (positional)

  const cartItems = await page.locator('.cart-item').all() // 👎🏻 violates A.58 (custom loop)
  for (const item of cartItems) expect(await item.isVisible()).toBe(true) // 👎🏻 violates A.58

  await page.goto('https://stripe.com/confirm') // 👎🏻 violates A.23 (external system)
  expect(await page.evaluate(() => localStorage.getItem('orderId'))).toBeTruthy() // 👎🏻 violates A.14 (implementation detail)
})
```

### GOOD E2E Test Example

📝 Note: While this test below is considered a good system-wide, end to end test, these patterns are not adequate for inegration and unit tests - practices for these type of tests are described in the document @testing-best-practices.md

```typescript
test('Checkout flow', async ({ page }) => {
  const customer = await saveNewCustomer(customer)
  const product = await saveNewProduct(product)
  await navigateToProductsPage(page)
  await selectProduct(page, product)
  await goToCheckout(page)
  await fillCustomerDetails(page, customer)
  await fillPaymentDetails(page, buildCreditCard())
  const orderDetails = await submitOrderAndVerifySuccessPage(page)
  await verifyOrderConfirmationEmailSent(customer.email, orderDetails)
  await verifySupplyRequestWasCreated(customer.email, orderDetails)
})
```

