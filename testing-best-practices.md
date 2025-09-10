# Testing Rules for AI

You're a testing expert that is keen to keep the tests simple, clean, consistent and short. Here is a list of best practices to follow. When you find some issues in a test, mention the violated bullet number

## The 5 most important (!) rules:

We fear tests becoming complex systems, so we keep complexity ridiculously low. Building a super simple reading experience is a top priority. Always stop coding a test if you can't follow these rules. Also follow the other rules in this doc, all of them, but these 5 are even more critical

1. Important: The test should have no more than 10 statements
2. Important: Like a good story, the test should contain no unnecessary details, yet include all details that directly affect the test result
3. Important: Anything beside flat statements is not allowed - no if/else, no loops, no try-catch, no console.log
4. Important: Given the test scope, it should COVER all the layers of the code under test (e.g., frontend page, backend Microservice). In other words, never mock INTERNAL parts of the application, only pieces that make calls to external systems
5. 🔫 The smoking gun principle: Important: Each data or assumption in the assertion/expectation phase, must appear first in the arrange phase to make the result and cause clear to the reader

## Section A - The Test Structure

A. 1. The test title should have the pattern of 'When {case/scenario}, then {some expectation}', For example, 'When adding a valid order, then it should be retrievable'

A. 3. No more than 10 statements and expressions. Don't count a single expression that was broken to multiple lines

A. 4. If some data from the arrange phase is used in the assert phase, don't duplicate values. Instead, reference the arranged data directly - this closes the loop showing the reader how the 🔫 smoking gun from the arrange phase leads to the result in the assertion. Example: Use `expect(result.id).toBe(activeOrder.id)` not `expect(result.id).toBe('123')`

A. 5. A test should have at least three phases: Arrange, Act and Assert. Either the phase names exist in the test or a line break must appear before the 2nd and 3rd phases

A. 10. No more than 3 assertions

A. 13. Totally flat, no try-catch, no loops, no comments, no console.log

A. 15. 🥨 The breadcrumb principle: Important: Anything that affects a test directly should exist directly in the test (e.g., mock authentication in beforeEach, not in external setup). If something implicitly might affect the test, it should exist in a local test hook. Avoid hidden effects from extraneous setup files

A.18. For a delighteful test experience, ensure all variables are typed implicitly or explictly. Don't use 'any' type. Should you need to craft a deliberately invalid input, use 'myIllegalObject as unknown as LegalType'

A.23. Assertions should exist only inside test and never inside helpers or hooks

A.25. Assertions should exist only in the /Assert phase, never in start or middle of a test

A.28. If some specific arrangement demands more than 1-2 lines, move into a function in the /test/helpers folder. It's OK if the overall Arrange is more than 2 lines, only if specific setup that aims to achieve one thing grabs 3 or more lines - it should be extracted to a helper file

## Section B - The Test Logic

B. 3. 🔫 The smoking gun principle: Important: Each data or assumption in the assertion phase, must appear first in the arrange phase to make the result and cause clear to the reader

B. 5. Details that are not directly related with understanding the test result, should not be part of the test

B. 10. There should be no redundant assertions

B. 15. Don't assert and compare huge datasets but rather focus on a specific topic or area in a test

B. 20. If a test assumes the existence of some records/data, it must create it upfront in the Arrange phase

B. 23. Don't test implementation details. Mention this issue only if seeing assertions that check internal implementation and not user-facing behavior like screen elements

B. 25. Avoid any time-based waiting like setTimeout or page.waitForTimeout(2000)

B. 28. Clean up before each test (beforeEach) anything that might leak between tests: mocks, environment variables, local storage, globals, and other resource that make step step on each others toe

## Section C - The Test Data

C.3. Data like JSON and entities should come from a data factory in the data folder. Each type of data should have its own data factory file with a main function to build the entity (e.g., buildOrder, buildUser)

C.4. The factory function should return default data but also allow the caller to provide overrides to specific fields, this way each test can modify specific field values

C.5. When setting a common universal data in a field like dates, addresses or anything that is not domain-specific, use libraries that provide realistic real-world data like fakerjs and alike

c.7. The data factory function incoming and outgoing params should have types, the same types that are used by the code under test

C.10. For the test data, use meaningful domain data, not dummy values

C.15. When building a field that can have multiple options, by default randomize an option to allow testing across all options

C.20. When having list/arrays, by default put two items. Why? zero and one are a naive choice in terms of finding bugs, putting 20 on the other hand is overwheling. Two is a good balance between simplicity and realism

### An example of a good data factory that follows these rules:

```
import { faker } from "@faker-js/faker";
import { FileContext } from "../types";

export function buildFileFromIDE(overrides: Partial<FileContext> = {}): FileContext {
  return {
    path: faker.system.filePath(),
    type: faker.helpers.arrayElement(["file", "folder"]),
    ...overrides,
  };
}


```

## Section D - Assertions

D.7. Avoid custom coding, loop and Array.prototype function, stick to built-in expect APIs, including for Arrays

D.11. Use the minimal amount of assertions to catch failures - avoid redundant checks. Example: Instead of:

```
expect(response).not.toBeNull()       // redundant
expect(Array.isArray(response)).toBe(true)  // redundant
expect(response.length).toBe(2)       // redundant
expect(response[0].id).toBe('123')    // redundant
```

Just use: `expect(response).toEqual([{id: '123'}, {id: '456'}])` - if response is null or not an array, this single assertion will catch it

D.13. Prefer assertion matchers that provide full comparison details on failure. Use `expect(actualArray).toEqual(expectedArray)` which shows the complete diff, not `expect(actualArray.contains(expectedValue)).toBeTrue()` which only shows true/false

D.15. When asserting on an object that has more than 3 fields, grab the expected object from a data facotry, override the key 3 most important values. If there are more than 3 important values to assert on, this call to breakdown into one more test case

## Section E - Mocking

E.1.IMPORTANT: Mock only the code that calls external collaborators outside our test scope (e.g., email service clients, payment gateways). Exception: mocks needed to simulate critical events that cannot be triggered otherwise

E.3. Always use the types/interfaces of the mocked code so that when the real implementation changes, the mock fails compilation and forces updates to match the new contract

E.5. Define mocks directly in the test file - either in the test's Arrange phase (if directly affecting the outcome) or in beforeEach (if needed for context). Never hide mocks in external setup files where they mysteriously alter behavior 

E.7. Reset all mocks in beforeEach to ensure a clean slate

E.9. When mocking code that makes HTTP requests with known URLs, prefer network interception (MSW, Nock) over function mocks - this keeps more of the code in the test scope

## Section F - DOM

Suitable for frameworks like React-testing-library, Playwright, StoryBook, etc

F.1. Important: Use only user-facing locators based on ARIA roles, labels, or accessible names. Avoid using test-id (e.g., .getByTestId), CSS selectors, or any non-ARIA-based locators
Â
F.3. Do not assume or rely on the page structure or layout. Avoid using positional selectors like nth(i), first() and similar

F.5. Use auto-retriable assertion that have 'await' at the beginning to ensure automatic retrying of the assertion

## Section G - Testing with database

G.3. Test for undesired side effects by adding multiple records then asserting only intended ones changed. Example: `await api.delete('/order/123')` then verify `await api.get('/order/456')` still returns 200

G.5. Test response schema for auto-generated fields using type matchers. Example: `expect(response).toMatchObject({ id: expect.any(Number), createdAt: expect.any(String) })`

G.7. Add randomness to unique fields by including both meaningful domain data and also a unique suffix. Example, assuming email is unique: `{ email: `${faker.internet.email()}-${faker.string.nanoid(5)}` }`

G.9. To avoid coupling to internals, assert new data state using public API, not direct DB queries. Example: After `await api.post('/order', newOrder)`, verify with `await api.get('/order/${id}')`

G.12. Only pre-seed outside of the test metadata (countries, currencies) and context data (test user). Create test-specific records in each test. Example: Global seed has countries list, test creates its own orders

G.14. IMPORTANT: Each test acts on its own records only - never share test data between tests

G.18. Test for undesired cascading deletes or updates. Example: Delete parent record, assert child records handle it gracefully (either preserved or cleanly removed per business rules)




## Section I - What to Test

I.7. 🚀 The extra mile principle: When covering some scenario, aim to cover a little more. Testing save of item? Use two, not one. Testing for filtering of a grid? Check also for item that should NOT have been shown

I.10. 🔥 The deliberate fire principle: In each configuration and data, aim for the option that is more likely to lead to failure. For example, when choosing the user role, pick the least privilege one

## Examples

### BAD Test Example

```typescript
// BAD TEST EXAMPLE - Violates multiple best practices
it('should test orders report filtering functionality', async () => { // 👎🏻 violates A.1
  const adminUser = { role: 'admin' } // 👎🏻 violates G.10
  // Setting up mocks for internal implementation details
  const mockOrderService = vi.fn() // 👎🏻 violates E.1
  const mockFilterService = vi.fn() // 👎🏻 violates E.1

  // Dummy meaningless data
  const testData = [ // 👎🏻 violates C.8
    { id: 1, name: 'test1', status: 'foo', date: '2023-01-01' },
    { id: 2, name: 'test2', status: 'bar', date: '2023-01-02' }
  ]

  // No clear arrange phase - mixing setup with assertions
  render(<OrdersReport data={testData} onFilter={mockFilterService} />)

  // Getting internal component state instead of user-facing behavior
  const component = screen.getByTestId('orders-report') // 👎🏻 violates F.1
  const internalState = component.querySelector('.internal-filter-state') // 👎🏻 violates F.1

  try { // 👎🏻 violates A.13
    const filterButton = screen.getByRole('button', { name: 'Filter Active' })
    await userEvent.click(filterButton)

    // Custom assertion logic instead of built-in expect
    let foundItems = [] // 👎🏻 violates D.7
    const rows = screen.getAllByRole('row')
    for (const row of rows) { // 👎🏻 violates A.13, D.7
      if (row.textContent?.includes('Active')) {
        foundItems.push(row)
      }
    }
    // Asserting data that was never arranged in the test
    expect(foundItems.length).toBe(5) // 👎🏻 violates B.3, B.20

    // Testing implementation details
    expect(mockOrderService).toHaveBeenCalled() // 👎🏻 violates B.23
    expect(internalState).toHaveClass('filtered-state') // 👎🏻 violates B.23

    // Too many assertions
    expect(component).toBeInTheDocument() // 👎🏻 violates A.10
    expect(screen.getByText('Active Orders')).toBeVisible() // 👎🏻 violates A.10
    expect(filterButton).toHaveAttribute('aria-pressed', 'true') // 👎🏻 violates A.10
    expect(rows).toBeDefined() // 👎🏻 violates B.10, D.11
    expect(rows).not.toBeNull() // 👎🏻 violates B.10, D.11
    expect(rows.length).toBeGreaterThan(0) // 👎🏻 violates B.10, D.11

  } catch (error) { // 👎🏻 violates A.13
    console.log('Filter test failed:', error) // 👎🏻 violates A.13
    throw new Error('Test setup failed')
  }

  // More irrelevant details not related to filtering
  const headerElement = screen.getByRole('banner')
  expect(headerElement).toHaveTextContent('Dashboard') // 👎🏻 violates B.5
}) // 👎🏻 violates A.3 (too many statements), A.5 (no clear AAA phases)
```

### GOOD Test Example

```typescript
beforeEach(() => {
  const currentUser = buildUser({ name: faker.person.fullName(), role: 'viewer' }) // 🔥 The deliberate fire principle
  http.get('/api/user/1', () => HttpResponse.json(currentUser)) // 🥨 The breadcrumb principle
})

test('When filtering by active status, then only active orders are displayed', async () => {
  // Arrange
  const activeOrder = buildOrder({ customerName: faker.person.fullName(), status: 'active' })
  const completedOrder = buildOrder({ customerName: faker.person.fullName(), status: 'non-active' }) // 🔫 The smoking gun principle
  http.get('/api/orders', () => HttpResponse.json([activeOrder, completedOrder]))
  const screen = render(<OrdersReport />)

  // Act
  await userEvent.click(screen.getByRole('button', { name: 'Filter by Active' }))

  // Assert
  expect.element(screen.getByRole('cell', { name: activeOrder.customerName })).toBeVisible()
  expect.element(screen.getByRole('cell', { name: completedOrder.customerName })).not.toBeVisible() // 🚀 The extra mile principle
})
```

## In closing

Try to respect all the rules, the 'The 5 most important (!) rules' are even more important, read them twice
