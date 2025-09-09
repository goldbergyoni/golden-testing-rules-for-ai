Hey dear assistant, based on the attached OpenAPI, please create two separate lists: The first with test case titles only (no code), the 2nd list with the test code. Generate these only for the route name "postUserByUserIdTickets"

This URL below will tell you which test cases to produce. If you have an idea for other tests cases that are really valuable, specify no more then 3 of your own ideas and group them in a dedicated section

Note that the guide in the provided URL contains best practices for tests writing, please use it

Overall, the basic test template should follow this pattern:
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

Generate all the test cases that are specified in this url below

Here is the guide:
https://github.com/goldbergyoni/reliable-ai-testing/blob/main/great-component-tests.md
