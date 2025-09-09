# Generate clean and comprehensive component tests

⏰ WIP, draft, writing now during October

## Best practice test

Here is a golden template for a good test, please use this style. Note that the generated tests should follow this structure and practices:

```typescript
// Note the test title pattern
test("When adding a new valid order without delivery address, then ensure it was saved correctly", async () => {
        // Arrange
        const orderToAdd = buildOrderData({deliveryAddress: undefined}) // Note how the cause is set explicitly and not outside

        // Act
        const receivedResponse = await testSetup.getHTTPClient().post("/api/orders", orderToAdd);

        // Assert
        expect(receivedResponse.status).toBe(200);
        const savedOrder = await testSetup.getHTTPClient().get(`/api/order/${receivedResponse.data.id}`); // Note how not only the response status is checked but rather the new state
        expect(receivedResponse.data).toMatchObject({orderToAdd})
      });
```


## Which tests to generate

For each route in the provided OpenAPI, generate the following test cases. The test title should have the pattern of 'When {case/scenario}, then {some expectation}', For example, 'When adding a valid order, then it should be retrievable'


1. Happy path - Provide valid values, ensure the result schema and status are as described in the OpenAPI
2. Each possible response - For each documented HTTP status response, please include a test that simulates the condition to trigger this response and assert that indeed the right response is returned
3. Bad request body - For each mandatory field, create a test for 'When {field name} is empty, then 400 error is returned and no error is saved'
4. Unauthorized test - If the route has a security schema, call it with a valid payload but compromise the security requirements and expect to get 401 or 403, based on the spec
5. The new state - If a post route, ensure that the new state is as expected. For example, create a test 'when adding a new order, then the saved order is retrievable'
6. Avoid overdoing - In case of delete/update routes, create two records and then edit/delete one, then ensure that the other one is not affected
7. If you have other ideas not listed below, only if they are highly valuable, include these in a dedicated section 'Other ideas'
_5. 20 other instructions soon_
