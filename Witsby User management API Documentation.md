# API Documentation

​
This API documentation outlines the available GraphQL queries and mutations for managing institutions, schools and districts information. It provides the ability to create, update, retrieve, and remove details. Authentication is required for all operations and rate limits may apply based on the server configuration.

Please find below the detailed documentation for each query and mutation, along with their expected request formats, response formats, error handling, and test scenarios.

## SalesForce (SF) APIs

### Account Creation using SF

​

1. **Create Witsby Subscription Record**
   ​
   - **Endpoint**: `/sfapi/v1/witsby-subscription`
   - **Method**: `POST`
   - **Payload**:
     ​
     ```json
     {
       "subscriptionId": "string",
       "accountName": "string"
     }
     ```
     ​
   - **Response**: Standard API responses for success or error.
2. **Create Term Record & Update Term Date**
   ​
   - **Endpoint**: `/sfapi/v1/witsby-subscription/term`
   - **Method**: `POST`
   - **Payload**:
     ​
     ```json
     {
       "subscriptionId": "string",
       "termDate": "date"
     }
     ```
     ​

### Fetch Data from AgileED

​

1. **Fetch AgileED Data**
   - **Endpoint**: `/sfapi/v1/agileed/data`
   - **Method**: `GET`
   - **Response**: Returns the data fetched from AgileED.
     ​

## Witsby GraphQL API​

#### **Endpoint**: `https://api.witsby.ascd.org/graphql`

​

### Account Creation using Witsby

​

#### 1. Create Institution Account

This API provides a mutation to create a new institution.When new deal is `closed won` Sales Force will call this Witsby API. It allows to specify various details, including the institution's name, location, subscription information, status, total seat licenses purchased, etc.

- **GraphQL Mutation**:
  ​
  ```graphql
      mutation CreateInstitution($createInstitutionInput: CreateInstitutionInput!) {
        createInstitution(createInstitutionInput: $createInstitutionInput) {
          _id
          name
          iconSrc
          subscriberType
          ascdId
          subscriptionInfo
          city
          state
          postalCode
          country
          status
          salesforceAccountID
          totalSeatLicensesPurchased
        }
      }
    ```
  ​
- **Input Type**:
  ​
  ```graphql
      input CreateInstitutionInput {
        name: String!
        subscriberType: String!
        ascdId: String!
        subscriptionInfo: JSON!
        city: String!
        state: String!
        postalCode: String!
        country: String!
        email: String
        phoneNumber: String
        status: "ACTIVE" || "INACTIVE" || "TRIAL" || "PENDING_SETUP"!
        salesforceAccountID: String!
        totalSeatLicensesPurchased: Int!
      }
    ```
  ​
- **Input Validation**:
  ​
  - **name**:
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **subscriberType**:
    - Type: String
    - Required: Yes
    - Validation: Should be one of ["DISTRICT", "SCHOOL"].
  - **ascdId (Witsby contract id)**:
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **subscriptionInfo**
    - Type: JSON
    - Required: Yes
    - Validation: Should content `startDate` and `expiryDate`.
    - **Example Value**:
     ​
      ```json
      {
        "startDate": "2023-12-10T00:00:00.000+05:30",
        "expiryDate": "2024-12-08T00:00:00.000+05:30"
      }
      ```
  - **city**
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **state**
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **postalCode**
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **country**
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **status**:
    - Type: String
    - Required: Yes
    - Validation: Should be one of ["ACTIVE", "INACTIVE", "TRIAL", "PENDING_SETUP"].
  - **email**:
    - Type: String
    - Validation: Must be a valid email format.
  - **phoneNumber**:
    - Type: String
    - Validation: Should be a valid phone number format.
  - **salesforceAccountID**
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **totalSeatLicensesPurchased**
    - Type: Int
    - Required: Yes
    - Validation: Must be a valid number.
    ​
- **Headers**:
  ​
  - `Authorization`: Bearer token for authentication
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**:
  ​
  - 500 mutations per hour.
    ​
- **Expected Response Format**:
  ​
  ```json
  {
    "data": {
      "createInstitution": {
        "_id": "123",
        "name": "Example Institution",
        "iconSrc": "path/to/icon",
        "subscriberType": "School",
        "ascdId": "ASCD123",
        "subscriptionInfo": {
        "plan": "Premium",
        "startDate": "2023-01-01",
        "expiryDate": "2024-12-31"
        },
        "city": "Example City",
        "state": "CA",
        "postalCode": "12345",
        "country": "United States",
        "status": "PENDING_SETUP",
        "salesforceAccountID": "SF12345",
        "totalSeatLicensesPurchased": 300
      }
    }
  }
  ```
  ​
- **Error Handling**:
  ​
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
- **Deprecation Strategy**:
  ​
  - If there's any plan to deprecate fields or introduce breaking changes, these will be communicated in the API's changelog at least 3 months in advance.
  - Deprecated fields will be marked in the schema documentation and will still be available for 6 months post the deprecation announcement.
    ​
- **Test Scenarios**
  ​
  - Create an institution with valid data, user info and authorization.
  - Attempt mutation without providing required fields.
  - Attempt mutation without authorization.
  - Attempt mutation with an existing institution name (conflict scenario).
    ​
- **Test Cases**
  ​
  - **1.1 Create an institution with valid data and authorization**
    ​ - **Given**: User is authorized and sends a valid `Authorization` and `userinfo` header with valid institution data. - **When**: User sends a mutation request to create an institution. - **Then**: Server responds with status `200 OK` and returns the created or updated institution.
    ​
  - **1.2 Attempt mutation without providing required fields**
    ​ - **Given**: User is authorized but sends incomplete institution data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **1.3 Attempt mutation without authorization**
    ​ - **Given**: User does not send an `Authorization` header but provides valid institution data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **1.4 Attempt mutation with an existing institution name**
    ​ - **Given**: User is authorized and sends data of an institution name that already exists in the system. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​

#### 2. Update Institution Account (For renewal)

​
This API provides a mutation to update an existing institution's information. When deal is `renewed` Sales Force will call this Witsby API. It allows you to modify various details, including the institution's name, location, subscription information, and status.

- **GraphQL Mutation**:
  ​
  ```graphql
    mutation UpdateInstitution($updateInstitutionInput: UpdateInstitutionInput!) {
      updateInstitution(updateInstitutionInput: $updateInstitutionInput) {
        _id
        name
        iconSrc
        subscriberType
        ascdId
        subscriptionInfo
        city
        state
        postalCode
        country
        status
        totalSeatLicensesPurchased
        salesforceAccountID
      }
    }
  ```
  ​
- **Input Type**:
  ​
  ```graphql
    input UpdateInstitutionInput {
      id: ID!
      name: String
      subscriberType: String
      ascdId: String
      subscriptionInfo: JSON
      city: String
      state: String
      postalCode: String
      country: String
      email: String
      phoneNumber: String
      salesforceAccountID: String
      status: "ACTIVE" || "INACTIVE" || "TRIAL" || "PENDING_SETUP"
      totalSeatLicensesPurchased: Int
    }
  ```
  ​
- **Input Validation**:
  ​
  - **id**:
    - Type: ID
    - Required: Yes
    - Validation: Must be a valid institution ID.
  - **name**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **subscriberType**:
    - Type: String
    - Validation: Should be one of ["DISTRICT", "SCHOOL"].
  - **ascdId (Witsby contract id)**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **subscriptionInfo**
    - Type: JSON
    - Validation: Should content `startDate` and `expiryDate`.
    - **Example Value**:
     ​
      ```json
      {
        "startDate": "2023-12-10T00:00:00.000+05:30",
        "expiryDate": "2024-12-08T00:00:00.000+05:30"
      }
      ```
  - **city**
    - Type: String
    - Validation: Max-length of 150 characters.
  - **state**
    - Type: String
    - Validation: Max-length of 150 characters.
  - **postalCode**
    - Type: String
    - Validation: Max-length of 150 characters.
  - **country**
    - Type: String
    - Validation: Max-length of 150 characters.
  - **status**:
    - Type: String
    - Validation: Should be one of ["ACTIVE", "INACTIVE", "TRIAL", "PENDING_SETUP"].
  - **email**:
    - Type: String
    - Validation: Must be a valid email format.
  - **phoneNumber**:
    - Type: String
    - Validation: Should be a valid phone number format.
  - **salesforceAccountID**
    - Type: String
    - Validation: Max-length of 150 characters.
  - **totalSeatLicensesPurchased**
    - Type: Int
    - Validation: Must be a valid number.
    ​
- **Headers**:
  ​
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**:
  ​
  - 500 mutations per hour.
    ​
- **Expected Response Format**:
  ​
  ```json
    {
      "data": {
        "updateInstitution": {
          "_id": "123",
          "name": "Updated Institution Name",
          "iconSrc": "path/to/new/icon",
          "subscriberType": "University",
          "ascdId": "ASCD987",
          "subscriptionInfo": {
            "plan": "Basic",
            "startDate": "2023-02-01",
            "expiryDate": "2024-01-31"
          },
          "city": "New City",
          "state": "NY",
          "postalCode": "54321",
          "country": "United States",
          "status": "ACTIVE",
          "salesforceAccountID": "SF54321",
          "totalSeatLicensesPurchased": 320
        }
      }
    }
    ```
  ​
- **Error Handling**:
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
- **Deprecation Strategy**:
  ​
  - If there's any plan to deprecate fields or introduce breaking changes, these will be communicated in the API's changelog at least 3 months in advance.
  - Deprecated fields will be marked in the schema documentation and will still be available for 6 months post the deprecation announcement.
    ​
- **Test Scenarios**
  ​
  - Update an institution with valid data and authorization.
  - Attempt mutation without providing required fields.
  - Attempt mutation without authorization.
  - Attempt mutation with an existing institution name (conflict scenario).
    ​
- **Test Cases**
  ​
  - **2.1 Update an institution with valid data and authorization**
    ​ - **Given**: User is authorized and sends a valid `Authorization` header with valid account data. - **When**: User sends a mutation request to update an account. - **Then**: Server responds with status `200 OK` and returns the created or updated account.
    ​
  - **2.2 Attempt mutation without providing required fields**
    ​ - **Given**: User is authorized but sends incomplete account data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **2.3 Attempt mutation without authorization**
    ​ - **Given**: User does not send an `Authorization` header but provides valid account data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **2.4 Attempt mutation with an existing account name**
    ​ - **Given**: User is authorized and sends data of an account name that already exists in the system. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.

### Organization Hierarchy - CRUD Levels (structures) and Hierarchies

#### **1. Fetch Institution with Levels (structures) and Hierarchies**

​
API provides a query to retrieve detailed information about an institution based on its ID. The query returns various details, including the institution's basic information, subscription data, location, status, and additional hierarchical and structural data.
If a new institute is added, the status will be shown as `Pending Setup`.This will prompt the CST team to create the organization level and hierarchy.

- **GraphQL Query**:
  ​
  ```graphql
    query GetInstitution($institutionId: String, $ascdId: String) {
      institution(id: $institutionId, ascdId: $ascdId) {
        _id
        name
        iconSrc
        subscriberType
        ascdId
        subscriptionInfo
        city
        state
        postalCode
        country
        status
        salesforceAccountID
        totalSeatLicensesPurchased
        structure {
          id
          organizationId
          title
          description
          level
          type
          parentId
          createdAt
          updatedAt
        }
        hierarchies {
          id
          internalId
          title
          structureId
          sortingOrder
          parentId
          author {
            id
            name
            avatarUrl
            organization {
              id
              name
            }
          }
          lastUpdatedInfo {
            updatedAt
          }
          createdAt
        }
      }
    }
  ```

  - **Input Validation**:
  ​
    - **id**:
      - Type: ID
      - Required: Yes
      - Validation: Must be a valid institution ID.
    - **ascdId (Witsby contract id)**:
      - Type: String
      - Required: Yes
      - Validation: Max-length of 150 characters.
  ​
- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.
  ​
- **Expected Response Format**:
  ​

  ```json
  {
    "data": {
      "institution": {
        "_id": "123",
        "name": "Example Institution",
        "iconSrc": "path/to/icon",
        "subscriberType": "University",
        "ascdId": "ASCD123",
        "subscriptionInfo": {
          "plan": "Premium",
          "startDate": "2023-01-01",
          "expiryDate": "2024-12-31"
        },
        "city": "New City",
        "state": "NY",
        "postalCode": "54321",
        "country": "United States",
        "status": "PENDING_SETUP",
        "salesforceAccountID": "SF54321",
        "totalSeatLicensesPurchased": 300,
        "structure": [
          {
            "id": "71d377a1-558a-4257-ba4d-56a6d0bbbd3a",
            "organizationId": "63c7c48a3627a748a0cdee36",
            "title": "District",
            "description": "",
            "level": 1,
            "type": "DISTRICT",
            "parentId": "",
            "createdAt": "2023-10-25T10:26:27.192Z",
            "updatedAt": "2023-10-25T10:26:27.192Z"
          },
          {
            "id": "85744a9c-5af2-4fe5-a7bd-d04962275af9",
            "organizationId": "63c7c48a3627a748a0cdee36",
            "title": "Region",
            "description": "",
            "level": 2,
            "type": "OTHER",
            "parentId": "71d377a1-558a-4257-ba4d-56a6d0bbbd3a",
            "createdAt": "2023-10-25T10:26:27.192Z",
            "updatedAt": "2023-10-25T10:26:27.192Z"
          },
          {
            "id": "2b6323d9-6946-47b3-bd42-d34490cce7ad",
            "organizationId": "63c7c48a3627a748a0cdee36",
            "title": "Building",
            "description": "",
            "level": 3,
            "type": "SCHOOL",
            "parentId": "85744a9c-5af2-4fe5-a7bd-d04962275af9",
            "createdAt": "2023-10-25T10:26:27.192Z",
            "updatedAt": "2023-10-25T10:26:27.192Z"
          }
        ],
        "hierarchies": [
          {
            "id": "b3be1bb3-b487-4166-8886-e44c0eb3f01f",
            "internalId": "4215330",
            "title": "Millcreek Township School District",
            "structureId": "71d377a1-558a-4257-ba4d-56a6d0bbbd3a",
            "sortingOrder": 1,
            "parentId": "",
            "author": {
              "id": "00u6s6jgjdyUkZp6k1d7",
              "name": "John Doe",
              "avatarUrl": null,
              "organization": {
                "id": "63c7c48a3627a748a0cdee36",
                "name": "ASCD"
              }
            },
            "lastUpdatedInfo": {
              "updatedAt": "2023-01-31T13:12:13.000Z"
            },
            "createdAt": "2023-10-25T10:26:27.190Z"
          },
          {
            "id": "30200947-7883-4ff8-afdd-379db20062de",
            "internalId": "",
            "title": "North",
            "structureId": "85744a9c-5af2-4fe5-a7bd-d04962275af9",
            "sortingOrder": 1,
            "parentId": "b3be1bb3-b487-4166-8886-e44c0eb3f01f",
            "author": {
              "id": "00u6s6jgjdyUkZp6k1d7",
              "name": "John Doe",
              "avatarUrl": null,
              "organization": {
                "id": "63c7c48a3627a748a0cdee36",
                "name": "ASCD"
              }
            },
            "lastUpdatedInfo": {
              "updatedAt": "2023-01-31T13:12:18.000Z"
            },
            "createdAt": "2023-10-25T10:26:27.190Z"
          },
          {
            "id": "7051b68c-80c0-4105-8a87-f784c60bad4e",
            "internalId": "421533007320",
            "title": "McDowell Senior High School",
            "structureId": "2b6323d9-6946-47b3-bd42-d34490cce7ad",
            "sortingOrder": 1,
            "parentId": "671150b7-f941-4b11-9b48-28c7d5b98a43",
            "author": {
              "id": "00u6s6jgjdyUkZp6k1d7",
              "name": "John Doe",
              "avatarUrl": null,
              "organization": {
                "id": "63c7c48a3627a748a0cdee36",
                "name": "ASCD"
              }
            },
            "lastUpdatedInfo": {
              "updatedAt": "2023-01-31T13:12:41.000Z"
            },
            "createdAt": "2023-10-25T10:26:27.190Z"
          },
        ],
      }
    }
  }


  ```

  ​

- **Error Handling**:
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
- **Test Scenarios**
  - Retrieve Levels (structures) and Hierarchy information for the institution with valid authorization.
  - Retrieve institution information for the institution without authorization.
  - Test with an invalid token.
    ​
- **Test Cases**
  - 1.1 **Retrieve Levels (structures) and Hierarchy Information with Valid Authorization**
    - **Given**: User is authorized and sends a valid `Authorization` header.
    - **When**: User sends a query to retrieve institution information for the institution.
    - **Then**: Server responds with status `200 OK` and returns a list of levels.
  - 1.2 **Retrieve Institution Information without Authorization**
    - **Given**: User does not send an `Authorization` header.
    - **When**: User sends a query to retrieve institution information for the institution.
    - **Then**: Server responds with status `200 with an error message`.
  - 1.3 **Test with an invalid token**
    - **Given**: User sends an invalid `Authorization` header.
    - **When**: User sends a query to retrieve institution information for the institution.
    - **Then**: Server responds with status `200 with an error message`.

​

#### **2. Create/Update/Remove Level (structures)**

​
This API provides a mutation to create/update/remove the organization levels foThis API provides mutations for creating, updating, or removing organization `Levels` for institutions.

- **GraphQL Mutation**:
  ​

  ```graphql
    mutation UpdateInstitution($updateInstitutionInput: UpdateInstitutionInput!) {
      updateInstitution(updateInstitutionInput: $updateInstitutionInput) {
        _id
        name
        structure {
          id
          organizationId
          title
          description
          level
          type
          parentId
          createdAt
          updatedAt
        }
        hierarchies {
          id
          internalId
          title
          structureId
          sortingOrder
          parentId
          author {
            id
            name
            avatarUrl
            organization {
              id
              name
            }
          }
          lastUpdatedInfo {
            updatedAt
          }
          createdAt
        }
      }
    }

  ```

  - **Input Type**:
  ​
  ```graphql
    input UpdateInstitutionInput {
      id: ID!
      structure: [CreateInstitutionStructure!]
    }
  ```

  - **Input Validation**:
  ​
  - **id**:
    - Type: ID
    - Required: Yes
    - Validation: Must be a valid institution ID.
  - **structure**
    - Type: JSON
    - Validation: Should content valid structure JSON Object
    - **Example Value**:
     ​
      ```json
      [
            {
            "id": "456",
            "organizationId": "org123",
            "title": "District",
            "description": "Updated Structure Description",
            "level": "1",
            "type": "Department",
            "parentId": "0",
            "createdAt": "2023-01-01T12:00:00Z",
            "updatedAt": "2023-01-02T14:30:00Z"
          },
          {
            "id": "457",
            "organizationId": "org124",
            "title": "School",
            "description": "Updated Structure Description",
            "level": "2",
            "type": "Department",
            "parentId": "1",
            "createdAt": "2023-01-01T12:00:00Z",
            "updatedAt": "2023-01-02T14:30:00Z"
          },
        ]
      ```

​

- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**: E.g., 500 mutations per hour.
  ​
- **Expected Response Format**:
  ​

  ```json
  {
  "data": {
    "updateInstitution": {
        "_id": "123",
        "name": "Updated Institution Name",
        "structure": [
            {
            "id": "456",
            "organizationId": "org123",
            "title": "District",
            "description": "Updated Structure Description",
            "level": "1",
            "type": "Department",
            "parentId": "0",
            "createdAt": "2023-01-01T12:00:00Z",
            "updatedAt": "2023-01-02T14:30:00Z"
          },
          {
            "id": "457",
            "organizationId": "org124",
            "title": "School",
            "description": "Updated Structure Description",
            "level": "2",
            "type": "Department",
            "parentId": "1",
            "createdAt": "2023-01-01T12:00:00Z",
            "updatedAt": "2023-01-02T14:30:00Z"
          },
        ]
      }
    }
  }


  ```

  ​

- **Error Handling**:
  In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
  ​
- **Test Scenarios**
  - Create or Update a new level with valid data and authorization.
  - Create or Update a level without providing required fields.
  - Create or Update a level without authorization.
    ​
- **Test Cases**
  ​
  - 2.1 **Create or Update a new level with valid data and authorization** - **Given**: User is authorized and sends a valid `Authorization` header with valid level data. - **When**: User sends a `POST` request to create a level. - **Then**: Server responds with status `200 OK` and returns the created level.
    ​
  - 2.2 **Create or Update a level without providing required fields**
    ​ - **Given**: User is authorized but sends incomplete level data. - **When**: User sends a `POST` request to create a level. - **Then**: Server responds with status `200 with an error message`.
    ​
  - 2.3 **Create or Update a level without authorization**
    ​ - **Given**: User does not send an `Authorization` header but provides valid level data. - **When**: User sends a `POST` request to create a level. - **Then**: Server responds with status `200 with an error message`.
    ​

---

​

#### **3. Create/Update/Remove Hierarchies**

​
This API provides a mutation to create or update the organization hierarchy of an institution. This includes its basic details, organizational structure, and hierarchical information.

- **GraphQL Mutation**:
  ​
  ```graphql
    mutation UpdateInstitution($updateInstitutionInput: UpdateInstitutionInput!) {
      updateInstitution(updateInstitutionInput: $updateInstitutionInput) {
        _id
        name
        structure {
          id
          organizationId
          title
          description
          level
          type
          parentId
          createdAt
          updatedAt
        }
        hierarchies {
          id
          internalId
          title
          structureId
          sortingOrder
          parentId
          author {
            id
            name
            avatarUrl
            organization {
              id
              name
            }
          }
          lastUpdatedInfo {
            updatedAt
          }
          createdAt
        }
      }
    }
  ```

  - **Input Type**:
  ​
  ```graphql
    input UpdateInstitutionInput {
      id: ID!
      hierarchies: [CreateInstitutionHierarchies!]
    }
  ```

  - **Input Validation**:
  ​
  - **id**:
    - Type: ID
    - Required: Yes
    - Validation: Must be a valid institution ID.
  - **hierarchies**
    - Type: JSON
    - Validation: Should content valid hierarchies JSON Object
    - **Example Value**:
     ​
      ```json
      [
        {
          "id": "101",
          "internalId": "hier123",
          "title": "District Name",
          "structureId": "456",
          "sortingOrder": 1,
          "parentId": "102",
          "author": {
            "id": "author456",
            "name": "John Doe",
            "avatarUrl": "path/to/avatar",
            "organization": {
              "id": "org456",
              "name": "Example Org"
            }
          },
          "lastUpdatedInfo": {
            "updatedAt": "2023-01-03T10:15:00Z"
          },
          "createdAt": "2023-01-03T10:00:00Z"
        }
      ]
      ```


- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**: E.g., 500 mutations per hour.
  ​
- **Expected Response Format**:
  ​
  ```json
  {
    "data": {
      "updateInstitution": {
        "_id": "123",
        "name": "Updated Institution Name",
        "structure": {
          "id": "456",
          "organizationId": "org123",
          "title": "District",
          "description": "Updated Structure Description",
          "level": "1",
          "type": "Department",
          "parentId": "789",
          "createdAt": "2023-01-01T12:00:00Z",
          "updatedAt": "2023-01-02T14:30:00Z"
        },
        "hierarchies": [
          {
            "id": "101",
            "internalId": "hier123",
            "title": "District Name",
            "structureId": "456",
            "sortingOrder": 1,
            "parentId": "102",
            "author": {
              "id": "author456",
              "name": "John Doe",
              "avatarUrl": "path/to/avatar",
              "organization": {
                "id": "org456",
                "name": "Example Org"
              }
            },
            "lastUpdatedInfo": {
              "updatedAt": "2023-01-03T10:15:00Z"
            },
            "createdAt": "2023-01-03T10:00:00Z"
          }
        ]
      }
    }
  }
  ```
  ​
- **Error Handling**:
  In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.

- **Test Scenarios**
  - Create or Update a new hierarchies with valid data and authorization.
  - Create or Update a hierarchies without providing required fields.
  - Create or Update a hierarchies without authorization.
    ​
- **Test Cases**
  ​
  - 3.1 **Create or Update a new hierarchies with valid data and authorization** - **Given**: User is authorized and sends a valid `Authorization` header with valid level data. - **When**: User sends a `POST` request to create a level. - **Then**: Server responds with status `200 OK` and returns the created level.
    ​
  - 3.2 **Create or Update a hierarchies without providing required fields**
    ​ - **Given**: User is authorized but sends incomplete hierarchies data. - **When**: User sends a `POST` request to create a hierarchies. - **Then**: Server responds with status `200 with an error message`.
    ​
  - 3.3 **Create or Update a hierarchies without authorization**
    ​ - **Given**: User does not send an `Authorization` header but provides valid hierarchies data. - **When**: User sends a `POST` request to hierarchies a level. - **Then**: Server responds with status `200 with an error message`.

#### 4. Fetch Districts or School

While setting up the organization hierarchy this API provides a query to retrieve a list of custom District or School based on specified filters and record types. It returns comprehensive details about each custom District or School, including location, contact information, and attributes.

- **GraphQL Query**:

  ```graphql
    query GetCustomSchools($recordType: RecordType!, $filter: Filter!) {
      getCustomSchools(recordType: $recordType, filter: $filter) {
        schools {
          _id
          Inst_UID
          Institution_Name_Proper
          Mailing_Addr1_Proper
          Mailing_Addr2_Proper
          Mailing_City_Proper
          Mailing_State_Full
          Mailing_St
          Mailing_ZIP_5
          Mailing_ZIP
          Phone
          Main_Parent_UID
          Parent_UID
          Parent_Name_Proper
          Country
          Phone_Present
          Grade_LO
          Grade_Hi
          School_Type
          Record_Type
          Grade_Level
          Num_Schools
          Record_Type_Text
          School_Type_Text
          Grade_Level_Text
          NCES_LEAID
          SCHNO
          Date_Last_Changed
          Institution_Long_Name
          District_Type
          Fax
          URL
          School_Yr_Start
          School_Yr_End
          Latitude
          Longitude
          Date_Added
          Parent_Long_Name
          Area_Code
          Source
          Inst_Status
          Institution_Short_Name
          District_Type_Text
          Detailed_Grade_Level_Text
          Inst_Status_Text
          Main_Parent_Name_Proper
        }
        totalCount
      }
    }
  ```

- **Input Type**:
  ​
  ```graphql
    enum RecordType {
      Building
      District
    }

    input Filter {
      page: Int = 0
      limit: Int = 10
      sortField: String
      sortDirection: SortDirection
      searchField: [String!]
      searchText: String
    }
  ```

  - **Input Validation**:
  ​
  - **RecordType**:
    - Type: String
    - Required: Yes
    - Validation: Should be one of ["Building", "District"].
  - **Filter**
    - Type: JSON
    - Validation: Should content valid filter JSON Object
    - **Example Value**:
     ​
      ```json
      {
        "limit": 10,
        "page": 0,
        "searchField": "",
        "searchText": "",
        "sortDirection": "",
        "sortField": ""
      }
      ```

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql

- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.

- **Expected Response Format**:

  ```json
  {
    "data": {
      "getCustomSchools": {
        "schools": [
          {
            "_id": "123",
            "Inst_UID": "YourInstUID",
            "Record_Type_Text": "District",
            "Institution_Name_Proper": "District Name",
            "Mailing_Addr1_Proper": "Address Line 1",
          },
        ],
        "totalCount": 100
      }
    }
  }
  ```

- **Error Handling**:
- In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.

- **Test Scenarios**

  - Fetch Districts with valid authorization.
  - Fetch Districts without valid authorization.
  - Perform searches with different filter criteria.

- **Test Cases**

- 4.1 **Fetch Districts or School with valid authorization**

  - **Given**: User is authorized and sends a valid Authorization header.
  - **When**: User sends a GET request to fetch Districts or School.
  - **Then**: Server responds with status `200 OK` and returns a list of Districts or School.

- 4.2 **Fetch Districts or School without authorization**

  - **Given**: User does not send an Authorization header.
  - **When**: User sends a GET request to fetch Districts or School.
  - **Then**: Server responds with status `200 with an error message`.

- 4.3 **Test with an invalid token**

  - **Given**: User sends an invalid Authorization header.
  - **When**: User sends a GET request to fetch Districts or School.
  - **Then**: Server responds with status `200 with an error message`.

- 4.4 **Perform searches with different filter criteria**
  - Perform tests with various combinations of filter criteria to ensure accurate search results.

#### **5. Fetch Country Listing**

This query allows you to retrieve a list of unique countries available in the system while setting up organization hierarchy. It provides a simple and efficient way to obtain a list of countries for reference or selection in other parts of the application.

- **GraphQL Query**:

```graphql
   query {
     getUniqueCountries
   }
```

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql

- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.

- **Expected Response Format**:

  ```json
  {
    "data": {
      "getUniqueCountries": [
        "Canada",
        "China",
        "United Arab Emirates",
        "United States",
      ]
    }
  }

  ```

- **Error Handling**:
- In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.

- **Test Scenarios**

  - Fetch unique countries with valid authorization.
  - Fetch unique countries without authorization .
  - Test with an invalid token.

- **Test Cases**

- 5.1 **Fetch Unique Countries with Valid Authorization**

  - **Given**: User is authorized and sends a valid Authorization header.
  - **When**: User sends a GET request to fetch unique countries.
  - **Then**: Server responds with status `200 OK` and returns a list of unique countries.

- 5.2 **Fetch Unique Countries without Authorization**

  - **Given**: User does not send an Authorization header.
  - **When**: User sends a GET request to fetch unique countries.
  - **Then**: Server responds with status `200 OK` and returns a list of unique countries

- 5.3 **Test with an invalid token**
  - **Given**: User sends an invalid Authorization header.
  - **When**: User sends a GET request to fetch unique countries.
  - **Then**: Server responds with status `200 with an error message`.

#### \*\*6. Fetch State Listing

This query allows you to retrieve a list of unique states or regions within a specific country. You can specify the country as a parameter to obtain a list of states associated with that country. It provides a convenient way to fetch state or regional data for further use in your application.

- **GraphQL Query**:

```graphql
   query GetUniqueStates($country: String!) {
     getUniqueStates(country: $country)  "// e.g country : Canada
   }
```

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql

- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.

- **Expected Response Format**:

  ```json
  {
    "data": {
      "getUniqueStates": [
        "Quebec",
      ]
    }
  }
  ```

- **Error Handling**:
- In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.

- **Test Scenarios**

  - Fetch unique states for a country with valid authorization.
  - Fetch unique states for a country without authorization.
  - Test with an invalid token.

- **Test Cases**

- 6.1 **Fetch Unique States for a Country with Valid Authorization**

  - **Given**: User is authorized and sends a valid Authorization header.
  - **When**: User sends a GET request to fetch unique states for a specific country.
  - **Then**: Server responds with status `200 OK` and returns a list of unique states.

- 6.2 **Fetch Unique States for a Country without Authorization**

  - **Given**: User does not send an Authorization header.
  - **When**: User sends a GET request to fetch unique states for a specific country.
  - **Then**: Server responds with status `200 OK` and returns a list of unique states.

- 6.3 **Test with an invalid token**
  - **Given**: User sends an invalid Authorization header.
  - **When**: User sends a GET request to fetch unique states for a specific country.
  - **Then**: Server responds with status `200 with an error message`.

#### 7. Update Custom District or School

​
This mutation allows you to update the information of a District or school, including various attributes such as location, contact details, and educational parameters. The mutation takes an input with specific fields for updating District or school information. It provides a convenient way to make modifications to school records in the system.

- **GraphQL Mutation**:
  ​
  ```graphql
    mutation Mutation($updateSchoolInput: UpdateSchoolInput!) {
      updateSchool(updateSchoolInput: $updateSchoolInput) {
        _id
        Inst_UID
        Institution_Name_Proper
        Mailing_Addr1_Proper
        Mailing_Addr2_Proper
        Mailing_City_Proper
        Mailing_State_Full
        Mailing_St
        Mailing_ZIP_5
        Mailing_ZIP
        Phone
        Main_Parent_UID
        Parent_UID
        Parent_Name_Proper
        Country
        Phone_Present
        Grade_LO
        Grade_Hi
        School_Type
        Record_Type
        Grade_Level
        Num_Schools
        Record_Type_Text
        School_Type_Text
        Grade_Level_Text
        NCES_LEAID
        SCHNO
        Date_Last_Changed
        Institution_Long_Name
        District_Type
        Fax
        URL
        School_Yr_Start
        School_Yr_End
        Latitude
        Longitude
        Date_Added
        Parent_Long_Name
        Area_Code
        Source
        Inst_Status
        Institution_Short_Name
        District_Type_Text
        Detailed_Grade_Level_Text
        Inst_Status_Text
        Main_Parent_Name_Proper
      }
    }
  ```
  ​
- **Input Type**:
  ​
  ```graphql
    input UpdateSchoolInput {
      id: ID!
      Inst_UID: ID
      Institution_Name: String
      Inst_Status_Text: String
      NCES_LEAID:String
      SCHNO: String
    }
  ```
  ​
- **Input Validation**:

  - **id**:
    - Type: ID
    - Required: Yes
    - Validation: Must be a valid District or School ID.
  - **Inst_UID**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **Institution_Name**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **NCES_LEAID**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **SCHNO**:
    - Type: String
    - Validation: Max-length of 150 characters.
  - **Inst_Status_Text**:
    - Type: String
    - Validation: Should be one of ["Active", "Inactive"].
    ​
- **Headers**:
  ​
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**:
  ​
  - 500 mutations per hour.
    ​
- **Expected Response Format**:
  ​
  ```json
    {
      "data": {
        "updateSchool": {
          "_id": "123",
          "Inst_UID": "YourInstUID",
          "Institution_Name_Proper": "Updated School Name",
        }
      }
    }
    ```
  ​
- **Error Handling**:
  ​
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
- **Deprecation Strategy**:
  ​
  - If there's any plan to deprecate fields or introduce breaking changes, these will be communicated in the API's changelog at least 3 months in advance.
  - Deprecated fields will be marked in the schema documentation and will still be available for 6 months post the deprecation announcement.
    ​
- **Test Scenarios**
  ​
  - Update a District with valid data and authorization.
  - Attempt mutation without providing required fields.
  - Attempt mutation without authorization.
  - Test with an invalid token.
    ​
- **Test Cases**
  ​
  - **7.1 Update a District with valid data and authorization**
    ​ - **Given**: User is authorized and sends a valid `Authorization` header with valid account data. - **When**: User sends a mutation request to update a district's information. - **Then**: Server responds with status `200 OK` and returns the updated district information.
    ​
  - **7.2 Attempt mutation without providing required fields**
    ​ - **Given**: User is authorized but sends incomplete account data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **7.3 Attempt mutation without authorization**
    ​ - **Given**: User does not send an `Authorization` header but provides valid account data. - **When**: User sends a mutation request. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **7.4 Test with an Invalid Token**
    ​ - **Given**: User sends an invalid `Authorization` header. - **When**: User sends a mutation to update a district's information. - **Then**: Server responds with status `200 with an error message`.

#### 8. Create Custom District or School

​

- **GraphQL Mutation**:
  ​
  This mutation allows to create a custom school or district by providing various details such as location, contact information, and educational parameters. The mutation takes an input with specific fields to create a new school or district record in the system. It provides a convenient way to add custom educational entities to the system.

    ```graphql
    mutation Mutation($customSchoolAndDistrictInput: CustomSchoolAndDistrictInput!) {
      createCustomSchoolAndDistrict(CustomSchoolAndDistrictInput: $customSchoolAndDistrictInput) {
        _id
        Inst_UID
        Institution_Name_Proper
        Mailing_Addr1_Proper
        Mailing_Addr2_Proper
        Mailing_City_Proper
        Mailing_State_Full
        Mailing_St
        Mailing_ZIP_5
        Mailing_ZIP
        Phone
        Main_Parent_UID
        Parent_UID
        Parent_Name_Proper
        Country
        Phone_Present
        Grade_LO
        Grade_Hi
        School_Type
        Record_Type
        Grade_Level
        Num_Schools
        Record_Type_Text
        School_Type_Text
        Grade_Level_Text
        NCES_LEAID
        SCHNO
        Date_Last_Changed
        Institution_Long_Name
        District_Type
        Fax
        URL
        School_Yr_Start
        School_Yr_End
        Latitude
        Longitude
        Date_Added
        Parent_Long_Name
        Area_Code
        Source
        Inst_Status
        Institution_Short_Name
        District_Type_Text
        Detailed_Grade_Level_Text
        Inst_Status_Text
        Main_Parent_Name_Proper
      }
    }
  ```

  - **Input Type**:
  ​
  ```graphql
  input CustomSchoolAndDistrictInput {
    country: String!
    state: String!
    institutionName: String!
    LEAID: String!
    recordType: RecordType!
  }
  ````

  ​

- **Headers**:
  ​
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**:
  ​
  - 500 mutations per hour.
    ​
- **Expected Response Format**:
  ​
  ```json
    {
      "data": {
        "createCustomSchoolAndDistrict": {
          "_id": "123",
          "Inst_UID": "CUST123",
          "Institution_Name_Proper": "Custom School",
          "Mailing_Addr1_Proper": "123 Main Street",
          "Mailing_Addr2_Proper": "Suite 45",
          "Mailing_City_Proper": "New City",
          "Mailing_State_Full": "New York",
          "Mailing_St": "NY",
          "Mailing_ZIP_5": "54321",
          "Mailing_ZIP": "54321-6789",
          "Phone": "555-123-4567",
          "Main_Parent_UID": "PARENT123",
          "Parent_UID": "PARENT456",
          "Parent_Name_Proper": "Custom School District",
          "Country": "United States",
          "Phone_Present": "Yes",
          "Grade_LO": "K",
          "Grade_Hi": "12",
          "School_Type": "Public",
          "Record_Type": "Public",
          "Grade_Level": "Elementary",
          "Num_Schools": "1",
          "Record_Type_Text": "Public",
          "School_Type_Text": "Public",
          "Grade_Level_Text": "Elementary",
          "NCES_LEAID": "LEA123",
          "SCHNO": "SCHOOL456",
          "Date_Last_Changed": "2023-01-02",
          "Institution_Long_Name": "Custom School District",
          "District_Type": "Independent",
          "Fax": "555-789-1234",
          "URL": "https://www.customschool.org",
          "School_Yr_Start": "2023-08-15",
          "School_Yr_End": "2024-05-30",
          "Latitude": "40.7128",
          "Longitude": "-74.0060",
          "Date_Added": "2023-01-01",
          "Parent_Long_Name": "Custom School District",
          "Area_Code": "555",
          "Source": "Custom Data Source",
          "Inst_Status": "ACTIVE",
          "Institution_Short_Name": "Custom School",
          "District_Type_Text": "Independent",
          "Detailed_Grade_Level_Text": "K-12",
          "Inst_Status_Text": "Active",
          "Main_Parent_Name_Proper": "Custom School District",
        }
      }
    }
    ```

- **Error Handling**:
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
    ​
- **Test Scenarios**
  ​
  - Create a custom school or district with valid authorization.
  - Create a custom school or district without authorization.
  - Test with an invalid token.

​

- **Test Cases**
  ​
  - **8.1 Create Custom School/District with Valid Authorization**
    ​ - **Given**: User is authorized and sends a valid `Authorization` header with valid account data. - **When**: User sends a mutation to create a custom school or district. - **Then**: Server responds with status `200 OK` and returns the created custom school/district information.
    ​
  - **8.2 Create Custom School/District without Authorization**
    ​ - **Given**: User does not send an `Authorization` header. - **When**: User sends a mutation to create a custom school or district. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **8.3 Test with an Invalid Token**
    ​ - **Given**: User sends an invalid `Authorization `header. - **When**: User sends a mutation to create a custom school or district. - **Then**: Server responds with status `200 with an error message`.

#### 9. Remove Custom District or School

​

- **GraphQL Mutation**:
  ​
  This mutation allows to remove a school record from the system based on its unique identifier. Upon successful execution, the specified school's information will be deleted from the database.

    ```graphql
      mutation RemoveSchool($removeSchoolId: String!) {
        removeSchool(id: $removeSchoolId) {
          _id
          Inst_UID
          Institution_Name_Proper
          Mailing_Addr1_Proper
          Mailing_Addr2_Proper
          Mailing_City_Proper
          Mailing_State_Full
          Mailing_St
          Mailing_ZIP_5
          Mailing_ZIP
          Phone
          Main_Parent_UID
          Parent_UID
          Parent_Name_Proper
          Country
          Phone_Present
          Grade_LO
          Grade_Hi
          School_Type
          Record_Type
          Grade_Level
          Num_Schools
          Record_Type_Text
          School_Type_Text
          Grade_Level_Text
          NCES_LEAID
          SCHNO
          Date_Last_Changed
          Institution_Long_Name
          District_Type
          Fax
          URL
          School_Yr_Start
          School_Yr_End
          Latitude
          Longitude
          Date_Added
          Parent_Long_Name
          Area_Code
          Source
          Inst_Status
          Institution_Short_Name
          District_Type_Text
          Detailed_Grade_Level_Text
          Inst_Status_Text
          Main_Parent_Name_Proper
        }
      }
    ```

- **Headers**:
  ​
  - `Authorization`: Bearer token for authentication.
  - `userinfo`: Encrypted user object
  - `Content-Type`: application/graphql
    ​
- **Rate Limits**:
  ​
  - 500 mutations per hour.
    ​
- **Expected Response Format**:
  ​
  ```json
    {
      "data": {
        "removeSchool": {
          "_id": "123",
          "Inst_UID": "SCHOOL123",
          "Institution_Name_Proper": "Removed School",
          "Mailing_Addr1_Proper": "789 Elm Street",
          "Mailing_Addr2_Proper": "Suite 123",
          "Mailing_City_Proper": "Old Town",
          "Mailing_State_Full": "New York",
          "Mailing_St": "NY",
          "Mailing_ZIP_5": "54321",
          "Mailing_ZIP": "54321-1234",
          "Phone": "555-123-4567",
          "Main_Parent_UID": "PARENT123",
          "Parent_UID": "PARENT456",
          "Parent_Name_Proper": "Removed School District",
          "Country": "United States",
          "Phone_Present": "Yes",
          "Grade_LO": "K",
          "Grade_Hi": "12",
          "School_Type": "Public",
          "Record_Type": "Public",
          "Grade_Level": "Elementary",
          "Num_Schools": "1",
          "Record_Type_Text": "Public",
          "School_Type_Text": "Public",
          "Grade_Level_Text": "Elementary",
          "NCES_LEAID": "LEA789",
          "SCHNO": "SCHOOL123",
          "Date_Last_Changed": "2023-01-02",
          "Institution_Long_Name": "Removed School District",
          "District_Type": "Independent",
          "Fax": "555-789-1234",
          "URL": "https://www.removedschool.org",
          "School_Yr_Start": "2023-08-15",
          "School_Yr_End": "2024-05-30",
          "Latitude": "40.7128",
          "Longitude": "-74.0060",
          "Date_Added": "2023-01-01",
          "Parent_Long_Name": "Removed School District",
          "Area_Code": "555",
          "Source": "Custom Data Source",
          "Inst_Status": "REMOVED",
          "Institution_Short_Name": "Removed School",
          "District_Type_Text": "Independent",
          "Detailed_Grade_Level_Text": "K-12",
          "Inst_Status_Text": "Removed",
          "Main_Parent_Name_Proper": "Removed School District"
        }
      }
    }
    ```

- **Error Handling**:
  - In case of errors, this API follows the standard GraphQL practice. The HTTP status code for all responses is `200 OK`. To check for errors, inspect the `error` field in the response JSON. If present, it contains details about the encountered issues. Refer to the `Common Error Handling` section at the end of document for a comprehensive understanding of error handling.
    ​
- **Test Scenarios**
  ​

  - Remove a school with valid authorization.
  - Remove a school without authorization.
  - Test with an invalid token.

- **Test Cases**
  ​
  - **9.1 Remove School with Valid Authorization**
    ​ - **Given**: User is authorized and sends a valid `Authorization` header with valid account data. - **When**: User sends a mutation to remove school information. - **Then**: Server responds with status `200 OK` and returns the removed custom school/district information.
    ​
  - **9.2 Remove School without Authorization**
    ​ - **Given**: User does not send an `Authorization` header. - **When**: User sends a mutation to remove a remove school or district. - **Then**: Server responds with status `200 with an error message`.
    ​
  - **9.3 Test with an Invalid Token**
    ​ - **Given**: User sends an invalid `Authorization `header. - **When**: User sends a mutation to remove a custom school or district. - **Then**: Server responds with status `200 with an error message`.

#### Common Error Handling

- In GraphQL, the HTTP status code 200 OK is used for all responses, irrespective of whether they are successful or contain errors.
- To check for errors, inspect the `errors` field in the response JSON. If it is present, the request encountered one or more issues, and the `errors` field will contain details about each error.
- The `errors` field may include error messages, error codes, and additional information to help diagnose and resolve the problem.
- When an error occurs, you can typically find a description of the issue in the `message` field within each error object.
- Below is a general structure for describing error handling in your API documentation.
  ​
  - **GRAPHQL_PARSE_FAILED**: This error occurs when the GraphQL operation string contains a `syntax error`, making it impossible to parse the request.
  - **GRAPHQL_VALIDATION_FAILED**: This error indicates that the GraphQL operation is not valid against the `server's schema`server's schema. The request violates the schema's validation rules.
  - **BAD_USER_INPUT**: When the GraphQL operation includes an invalid value for a `field argument or input`, this error is used to signal that the user's input is incorrect or not allowed.
  - **PERSISTED_QUERY_NOT_FOUND**: This error is used in the context of automatic persisted queries (APQ). It occurs when a client sends the `hash` of a query string to execute via APQ, but the query was not found in the APQ cache.
  - **PERSISTED_QUERY_NOT_SUPPORTED**: This error occurs when a client sends the hash of a query string to execute via APQ, but the server has disabled automatic persisted queries.
  - **OPERATION_RESOLUTION_FAILURE**: This error is raised when the request is parsed successfully and is valid against the server's schema. Still, the server couldn't determine which named operation to run. This usually happens when a request contains multiple named operations and doesn't specify which operation to execute.
  - **BAD_REQUEST**: An error occurred before the server could attempt to `parse` the given GraphQL operation. This is a general error that indicates an issue with the request format or data.
  - **INTERNAL_SERVER_ERROR**: This error is used when an unspecified error occurred on the `server`, and no other specific error code is applicable.
