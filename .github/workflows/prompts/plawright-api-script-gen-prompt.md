---
description: Create automated API tests following the repo's existing test patterns and conventions using the linked APIM site documentation.
---

# Create API Test

Create automated API endpoint tests following the repo's existing test patterns and conventions using the linked APIM site documentation.

## Critical guardrails

- Treat the exact APIM URL provided in the prompt as the source of truth. Do not switch to a sibling endpoint, route, or operation unless the prompt explicitly requests it.
- After signing in to the APIM portal, navigate to the exact provided URL and confirm the operation name, HTTP method, route pattern, required auth, and response schema before writing any code.
- If the provided URL is for `/api/tenant/tenants/{adTenantId}`, do not implement tests for `/api/tenant/tenants` or any other similar operation unless the prompt explicitly asks for that operation.
- The agent must bind each test to the exact operation selected in the prompt. The prompt's URL and operation hash are more specific than any general API category or name.
- If there is ambiguity or the prompt does not provide a fully actionable URL, raise the issue for clarification instead of inferring a similar endpoint.

## Requirements

- Follow the style used by existing tests under `src/tests/api`.
- Use `apiTest(...)` from `src/tests/base/api-test.ts`.
- The linked APIM endpoint is the authoritative response contract. Use the exact operation method, path, and response contract from that page.
- APIM requires authentication. Email and password to log into the APIM will be provided as part of the prompt.
- Create a new test class for each distinct API endpoint.
- Models represent the shape of API data (requests, responses, or subcomponents of requests/responses), or Cosmos DB data. When matching, they can be shared.
- Model, step, and test files should be created in folders named for the API for which they are intended, for readability and organization purposes.
- API responses need to be validated against Cosmos DB data.
- To query Cosmos DB, use classes from `utils/cosmos-db`. There should be one Cosmos DB util class per Cosmos DB container.
- Reuse existing API step classes and request helpers where available.
- When data is modified in Cosmos DB (such as inserts, updates, or deletes) as part of the API endpoint under test, ensure that clean up is done to restore the database to its original state. This clean up can be done either via API endpoint calls (for example, calling a DELETE method to delete an intervention rule following a POST method to create an intervention rule) or directly restoring the Cosmos DB to its original state. This is not applicable for read-only operations such as GET requests.
- Keep tests focused on a single behavior per case.
- Include both positive and negative scenarios when relevant to the endpoint contract.
- Validate real HTTP behavior, not mock behavior.

## Process

1. Sign in to the APIM site using the provided email and password, then open the exact URL included in the prompt and confirm the operation name, route, method, auth, and response examples.
2. Bind the implementation to that exact operation and path only; do not substitute a related sibling endpoint.
3. Check if there is already a test class covering the endpoint so tests can be added to it/modified or if a new test class needs to be created.
4. Create or update Cosmos DB models as needed as per the structure of the particular Cosmos DB container data.
5. Add or update Cosmos utility classes as needed for querying the relevant Cosmos DB containers.
6. Create or update API endpoint-specific models as needed as per the structure of the API endpoint request and response.
7. Create or update the endpoint-specific step class if the code does not already exist.
8. Name each test using the project naming pattern such as `TC_#### - <short description of the test case>`. The number following the `TC_` should be a number of the test case in Azure DevOps corresponding to the automated test case being implemented here. For now, Azure DevOps test cases will be written manually and linked to the automated test cases by hand. The agent can therefore use `TC_####` as a placeholder when generating the test code.
9. Write assertions that validate real responses, including status, schema, and business behavior.
10. If the endpoint has error conditions, include negative tests for unauthenticated, unauthorized, invalid method, invalid route, and malformed requests when supported.
11. Run the focused test command for verification.

## Output

Provide:

- the new test file path
- a short summary of what the test covers
- any supporting step or model files added or updated
- the verification command run and the result
