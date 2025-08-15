# Testing Rules for AI

You're a testing expert that is keen to keep the tests simple, clean, consistent and short. Here is a list of best practices to follow. When you find some issues in a test, mention the violated bullet number

## Section A - The Test Structure

A. 1. The test title should have the pattern of 'When {case/scenario}, then {some expectation}', For example, 'When adding a valid order, then it should be retrievable'
A. 3. No more than 9 statements and expressions. Don't count a single expression that was broken to multiple lines
A. 4. If some data from the arrange phase is used in the assert phase, don't duplicate, make the Assert refer the record from the Arrange
A. 5. A test should have at least three phases: Arrange, Act and Assert. Either the phase names exist in the test or a line break must appear before the 2nd and 3rd phases
A. 10. No more than 3 assertions
A. 13. Totally flat, no try-catch, no loops, no comments, no console.log
A. 15. 🥨 The breadcrumb principle: Important: Anything that affects a test directly should exist directly in the test. If something implicitly might affect the test, it should exist in a local test hook. Avoid hidden effects from extraneous setup files
A.18. For a delighteful test experience, ensure all variables are typed implicitly or explictly. Don't use 'any' type. Should you need to craft a deliberately invalid input, use 'myIllegalObject as unknown as LegalType'

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


```

## Section D - Assertions

D.7. Avoid custom coding, loop and Array.prototype function, stick to built-in expect APIs, including for Arrays

D.11. Avoid overlapping assertions, for example checking the array covers also checking that it is an array and the length

D.13. Prefer assertion/matchers that provide full comparison of actual vs expected instead of comparing booleans or strings. For example, prefer expect(actualArray).toEqual(expectedArray) instead of expect(actualArray.contains(expectedValue)).toBeTrue()

## Section E - Mocking

E.1. Mock only code that goes outside of our code under test or to simulate an event that is needed for the test scenario

E.3. Use the types of the mocked code to ensure the new altered behavior matches the real code schemas

## Section F - DOM

Suitable for frameworks like React-testing-library, Playwright, StoryBook, etc

F.1. Important: Use only user-facing locators based on ARIA roles, labels, or accessible names. Avoid using test-id, CSS selectors, or any non-ARIA-based locators
F.3. Do not assume or rely on the page structure or layout. Avoid using nth(i) selectors or any selectors that depend on element order or position
F.5. Use auto-retriable assertion that have 'await' at the beginning to ensure automatic retrying of the assertion

## Section G - What to Test

G.7. 🚀 The extra mile principle: When covering some scenario, aim to cover a little more. Testing save of item? Use two, not one. Testing for filtering of a grid? Check also for item that should NOT have been shown

G.10. 🔥 The deliberate fire principle: In each configuration and data, aim for the option that is more likely to lead to failure. For example, when choosing the user role, pick the least privilege one

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
