# API Documentation
​
The following is the API documentation for the described services, organized by platform and functionality.
​
## SalesForce (SF) API
​
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
### Fetching Data from AgileED
​
1. **Fetch AgileED Data**
   - **Endpoint**: `/sfapi/v1/agileed/data`
   - **Method**: `GET`
   - **Response**: Returns the data fetched from AgileED.
​
## WITSBY GraphQL API
​
---
​
**Endpoint**: `/witsbyapi/graphql`
​
---
​
### Account Creation using Witsby
​
#### 1. Automatic Creation/Update of Account
​
- **GraphQL Mutation**:
​
    ```graphql
    mutation createOrUpdateAccount($account: AccountInput!) {
    createOrUpdateAccount(input: $account) {
        id
        name
        status
        createdAt
        updatedAt
    }
    }
    ```
​
- **Input Type**:
​
    ```graphql
    input AccountInput {
    id: ID
    name: String!
    status: String!
    email: String
    phoneNumber: String
    address: String
    ... [other fields]
    }
    ```
​
- **Input Validation**:
​
  - **name**:
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **status**:
    - Type: String
    - Required: Yes
    - Validation: Should be one of ["Active", "Inactive", "Pending"].
  - **email**:
    - Type: String
    - Validation: Must be a valid email format.
  - **phoneNumber**:
    - Type: String
    - Validation: Should be a valid phone number format.
  - **address**:
    - Type: String
    - Validation: Max-length of 500 characters.
​
- **Headers**:
​
  - `Authorization`: Bearer token for authentication.
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
            "createOrUpdateAccount": {
                "id": "123",
                "name": "Account Name",
                "status": "Active",
                "createdAt": "2023-10-13T12:00:00Z",
                "updatedAt": "2023-10-13T12:00:00Z"
            }
        }
    }
    ```
​
- **Error Handling**:
​
  - `400 Bad Request`: For validation errors, malformed GraphQL, or if required fields are missing.
  - `401 Unauthorized`: Invalid or missing token.
  - `409 Conflict`: If there's a conflict with existing data (e.g., duplicate account).
  - GraphQL-specific errors are encapsulated in the `errors` array in the response.
​
- **Deprecation Strategy**:
​
  - If there's any plan to deprecate fields or introduce breaking changes, these will be communicated in the API's changelog at least 3 months in advance.
  - Deprecated fields will be marked in the schema documentation and will still be available for 6 months post the deprecation announcement.
​
- **Sample Requests and Responses**:
​
  - **Request**:
​
    ```graphql
    mutation {
      createOrUpdateAccount(input: {name: "New Account", status: "Active", email: "account@example.com"}) {
        id
        name
        status
      }
    }
    ```
​
  - **Response**:
​
    ```json
    {
        "data": {
            "createOrUpdateAccount": {
                "id": "12345",
                "name": "New Account",
                "status": "Active"
            }
        }
    }
    ```
​
- **Test Scenarios**
​
  - Create or update an account with valid data and authorization.
  - Attempt mutation without providing required fields.
  - Attempt mutation without authorization.
  - Attempt mutation with an existing account name (conflict scenario).
​
- **Test Cases**
​
  - **1.1 Create or update an account with valid data and authorization**
​
    - **Given**: User is authorized and sends a valid `Authorization` header with valid account data.
    - **When**: User sends a mutation request to create or update an account.
    - **Then**: Server responds with status `200 OK` and returns the created or updated account.
​
  - **1.2 Attempt mutation without providing required fields**
​
    - **Given**: User is authorized but sends incomplete account data.
    - **When**: User sends a mutation request.
    - **Then**: Server responds with status `400 Bad Request`.
​
  - **1.3 Attempt mutation without authorization**
​
    - **Given**: User does not send an `Authorization` header but provides valid account data.
    - **When**: User sends a mutation request.
    - **Then**: Server responds with status `401 Unauthorized`.
​
  - **1.4 Attempt mutation with an existing account name**
​
    - **Given**: User is authorized and sends data of an account name that already exists in the system.
    - **When**: User sends a mutation request.
    - **Then**: Server responds with status `409 Conflict`.
​
### Organization Hierarchy - CRUD Levels
​
#### **1. Fetch Levels (READ)**
​
- **GraphQL Query**:
​
  ```graphql
  query {
    hierarchyLevels {
      id
      name
      description
    }
  }
  ```
​
- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
​
- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.
​
- **Expected Response Format**:
​
  ```json
  {
      "data": {
          "hierarchyLevels": [
              {
                  "id": "123",
                  "name": "Level 1",
                  "description": "Description of level 1"
              },
              ...
          ]
      }
  }
  ```
​
- **Error Handling**:
  - `400 Bad Request`: If the GraphQL query is malformed.
  - `401 Unauthorized`: Invalid or missing token.
  - GraphQL errors are encapsulated in the `errors` array in the response.
​
- **Test Scenarios**
  - Fetch all levels with valid authorization.
  - Fetch levels without authorization.
  - Test with an invalid token.
​
- **Test Cases**
  - 1.1 **Fetch all levels with valid authorization**
    - **Given**: User is authorized and sends a valid `Authorization` header.
    - **When**: User sends a `GET` request to fetch levels.
    - **Then**: Server responds with status `200 OK` and returns a list of levels.
  - 1.2 **Fetch levels without authorization**
    - **Given**: User does not send an `Authorization` header.
    - **When**: User sends a `GET` request to fetch levels.
    - **Then**: Server responds with status `401 Unauthorized`.
  - 1.3 **Test with an invalid token**
    - **Given**: User sends an invalid `Authorization` header.
    - **When**: User sends a `GET` request to fetch levels.
    - **Then**: Server responds with status `401 Unauthorized`.
​
---
​
#### **2. Create Level (CREATE)**
​
- **GraphQL Mutation**:
​
  ```graphql
  mutation {
    createHierarchyLevel(input: {name: "Level Name", description: "Description for this level"}) {
      id
      name
      description
    }
  }
  ```
​
- **Validation**:
  - **Input**:
    - `name`: Required, string, max-length: 100.
    - `description`: Optional, string, max-length: 500.
  - Ensure the input does not contain malicious code or SQL injections.
​
- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
​
- **Rate Limits**: E.g., 500 mutations per hour.
​
- **Expected Response Format**:
​
  ```json
  {
      "data": {
          "createHierarchyLevel": {
              "id": "123",
              "name": "Level Name",
              "description": "Description for this level"
          }
      }
  }
  ```
​
- **Error Handling**:
  - `400 Bad Request`: For validation errors, malformed GraphQL.
  - `401 Unauthorized`: Invalid or missing token.
  - `409 Conflict`: If a level with the same name already exists.
  - GraphQL errors (like validation issues) are encapsulated in the `errors` array in the response.
​
- **Test Scenarios**
  - Create a new level with valid data and authorization.
  - Create a level without providing required fields.
  - Create a level without authorization.
  - Create a level with an existing name.
​
- **Test Cases**
​
  - 2.1 **Create a new level with valid data and authorization**
    - **Given**: User is authorized and sends a valid `Authorization` header with valid level data.
    - **When**: User sends a `POST` request to create a level.
    - **Then**: Server responds with status `200 OK` and returns the created level.
​
  - 2.2 **Create a level without providing required fields**
​
    - **Given**: User is authorized but sends incomplete level data.
    - **When**: User sends a `POST` request to create a level.
    - **Then**: Server responds with status `400 Bad Request`.
​
  - 2.3 **Create a level without authorization**
​
    - **Given**: User does not send an `Authorization` header but provides valid level data.
    - **When**: User sends a `POST` request to create a level.
    - **Then**: Server responds with status `401 Unauthorized`.
​
  - 2.4 **Create a level with an existing name**
​
    - **Given**: User is authorized and sends a name that already exists in the system.
    - **When**: User sends a `POST` request to create a level.
    - **Then**: Server responds with status `409 Conflict`.
​
---
​
#### **3. Update Level (UPDATE)**
​
- **GraphQL Mutation**:
​
  ```graphql
  mutation {
    updateHierarchyLevel(id: "123", input: {name: "Updated Level Name", description: "Updated description"}) {
      id
      name
      description
    }
  }
  ```
​
- **Validation**:
  - **Input**:
    - `id`: Required, unique identifier for the level.
    - `name`: Optional, string, max-length: 100.
    - `description`: Optional, string, max-length: 500.
  - Ensure the input does not contain malicious entities.
​
- **Headers**:
  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
​
- **Rate Limits**: E.g., 500 mutations per hour.
​
- **Expected Response Format**:
​
  ```json
  {
      "data": {
          "updateHierarchyLevel": {
              "id": "123",
              "name": "Updated Level Name",
              "description": "Updated description"
          }
      }
  }
  ```
​
- **Error Handling**:
  - `400 Bad Request`: For validation errors or malformed GraphQL.
  - `401 Unauthorized`: Invalid or missing token.
  - `404 Not Found`: If the level with the given `id` does not exist.
  - GraphQL errors are encapsulated in the `errors` array in the response.
​
- **Test Scenarios**
  - Update an existing level with valid data and authorization.
  - Update a level without providing required fields.
  - Update a level without authorization.
  - Update a non-existing level.
​
- **Test Cases**
​
  - 3.1 **Update an existing level with valid data and authorization**
    - **Given**: User is authorized, sends a valid `Authorization` header, and provides valid level data for an existing level.
    - **When**: User sends a `PUT` request to update a level.
    - **Then**: Server responds with status `200 OK` and returns the updated level.
​
  - 3.2 **Update a level without providing required fields**
    - **Given**: User is authorized but sends incomplete level data.
    - **When**: User sends a `PUT` request to update a level.
    - **Then**: Server responds with status `400 Bad Request`.
​
  - 3.3 **Update a level withou...

#### **4. Fetch Districts (READ)

- **GraphQL Query**:

 ```graphql

query {
  getCustomDistrict( {
    type: "DISTRICT"  
    filter: {
      page: 0       # Page number
      limit: 10     # Number of items per page
      sortField: "Institution_Name_Proper"  # Field to sort by
      sortDirection: ASC  # Sorting direction (ASC or DESC)
      searchField: ["Institution_Name_Proper"]  # Fields to search in
      searchText: "YourSearchText"  # Text to search for
    }
  }) {
    SchoolsOrDistrict {
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

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
  
- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.
  

- **Expected Response Format**:

  ```json
  {
      "data": {
        "getCustomDistrict": {
            "District": [
                {
                    "_id": "123",
                    "Inst_UID": "YourInstUID",
                    "Institution_Name_Proper": "District Name",
                    "Mailing_Addr1_Proper": "Address Line 1",
                    // Other fields...
                },
                // Additional Districts...
            ],
            "totalCount": 100
        }
    }
  }
  ```


- **Error Handling**:
 - `400 Bad Request`: If the GraphQL query is malformed.
 - `401 Unauthorized`: Invalid or missing token.
 - GraphQL errors are encapsulated in the `errors` array in the response.


- **Test Scenarios**
  - Fetch Districts with valid authorization.
  - Fetch Districts without valid authorization.
  - Perform searches with different filter criteria.

- **Test Cases**

- 4.1 **Fetch Districts with valid authorization**
    - **Given**: User is authorized and sends a valid Authorization header.
    - **When**: User sends a GET request to fetch Districts.
    - **Then**: Server responds with status 200 OK and returns a list of Districts.
  
- 4.2 **Fetch Districts without authorization**

    - **Given**: User does not send an Authorization header.
    - **When**: User sends a GET request to fetch Districts.
    - **Then**: Server responds with status 401 Unauthorized.

- 4.3 **Test with an invalid token**
    - **Given**: User sends an invalid Authorization header.
    - **When**: User sends a GET request to fetch Districts.
    - **Then**: Server responds with status 401 Unauthorized.

- 4.4 **Perform searches with different filter criteria**
    - Perform tests with various combinations of filter criteria to ensure accurate search results.

#### **5. Fetch Schools  (READ)

- **GraphQL Query**:

 ```graphql

query {
  getCustomSchools( {
    type: "SCHOOL"  
    filter: {
      page: 0       # Page number
      limit: 10     # Number of items per page
      sortField: "Institution_Name_Proper"  # Field to sort by
      sortDirection: ASC  # Sorting direction (ASC or DESC)
      searchField: ["Institution_Name_Proper"]  # Fields to search in
      searchText: "YourSearchText"  # Text to search for
    }
  }) {
    Schools {
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

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
  
- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.
  

- **Expected Response Format**:

  ```json
  {
      "data": {
        "getCustomSchools": {
            "Schools": [
                {
                    "_id": "123",
                    "Inst_UID": "YourInstUID",
                    "Institution_Name_Proper": "School Name",
                    "Mailing_Addr1_Proper": "Address Line 1",
                    // Other fields...
                },
                // Additional Schools...
            ],
            "totalCount": 100
        }
    }
  }
  ```


- **Error Handling**:
 - `400 Bad Request`: If the GraphQL query is malformed.
 - `401 Unauthorized`: Invalid or missing token.
 - GraphQL errors are encapsulated in the `errors` array in the response.


- **Test Scenarios**
  - Fetch Schools with valid authorization.
  - Fetch Schools without valid authorization.
  - Perform searches with different filter criteria.

- **Test Cases**

- 5.1 **Fetch Schools with valid authorization**
    - **Given**: User is authorized and sends a valid Authorization header.
    - **When**: User sends a GET request to fetch Schools.
    - **Then**: Server responds with status 200 OK and returns a list of Schools.
  
- 5.2 **Fetch Schools without authorization**

    - **Given**: User does not send an Authorization header.
    - **When**: User sends a GET request to fetch Schools.
    - **Then**: Server responds with status 401 Unauthorized.

- 5.3 **Test with an invalid token**
    - **Given**: User sends an invalid Authorization header.
    - **When**: User sends a GET request to fetch Schools.
    - **Then**: Server responds with status 401 Unauthorized.

- 5.4 **Perform searches with different filter criteria**
    - Perform tests with various combinations of filter criteria to ensure accurate search results.
  


  #### **6. Fetching COUNTRY LISTING (READ)

- **GraphQL Query**:

 ```graphql

query {
  getUniqueCountries
}

  ```

- **Headers**:

  - `Authorization`: Bearer token for authentication.
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
      // Additional countries...
    ]
    }
  }

  ```


- **Error Handling**:
 - `400 Bad Request`: If the GraphQL query is malformed.
 - `401 Unauthorized`: Invalid or missing token.
 - GraphQL errors are encapsulated in the `errors` array in the response.


- **Test Scenarios**
  - Fetch unique countries with valid authorization.
  - Fetch unique countries without authorization .
  - Test with an invalid token.

- **Test Cases**

- 6.1 **Fetch Unique Countries with Valid Authorization**
    - **Given**: User is authorized and sends a valid Authorization header.
    - **When**: User sends a GET request to fetch unique countries.
    - **Then**: Server responds with status 200 OK and returns a list of unique countries.
  
- 6.2 **Fetch Unique Countries without Authorization**

    - **Given**: User does not send an Authorization header.
    - **When**: User sends a GET request to fetch unique countries.
    - **Then**: Server responds with status 200 OK and returns a list of unique countries

- 6.3 **Test with an invalid token**
    - **Given**: User sends an invalid Authorization header.
    - **When**: User sends a GET request to fetch unique countries.
    - **Then**: Server responds with status 401 Unauthorized.


#### **6. Fetching STATE LISTING (READ)

- **GraphQL Query**:

 ```graphql

query GetUniqueStates($country: String!) {
  getUniqueStates(country: $country)  "// e.g country : Canada
}


  ```

- **Headers**:

  - `Authorization`: Bearer token for authentication.
  - `Content-Type`: application/graphql
  
- **Rate Limits**: Depending on server configuration, e.g., 1000 requests per hour.
  

- **Expected Response Format**:

  ```json
  {
  "data": {
    "getUniqueStates": [
      "Quebec",
      // Additional states or provinces...
    ]
  }
  }


  ```


- **Error Handling**:
 - `400 Bad Request`: If the GraphQL query is malformed.
 - `401 Unauthorized`: Invalid or missing token.
 - GraphQL errors are encapsulated in the `errors` array in the response.


- **Test Scenarios**
  - Fetch unique states for a country with valid authorization.
  - Fetch unique states for a country without authorization.
  - Test with an invalid token.

- **Test Cases**

- 6.1 **Fetch Unique States for a Country with Valid Authorization**
    - **Given**: User is authorized and sends a valid Authorization header.
    - **When**: User sends a GET request to fetch unique states for a specific country.
    - **Then**: Server responds with status 200 OK and returns a list of unique states.
  
- 6.2 **Fetch Unique States for a Country without Authorization**

    - **Given**: User does not send an Authorization header.
    - **When**: User sends a GET request to fetch unique states for a specific country.
    - **Then**: Server responds with status 200 OK and returns a list of unique states.

- 6.3 **Test with an invalid token**
    - **Given**: User sends an invalid Authorization header.
    - **When**: User sends a GET request to fetch unique states for a specific country.
    - **Then**: Server responds with status 401 Unauthorized.



#### 8. Update Custom School
​
- **GraphQL Mutation**:
​
    ```graphql
    mutation UpdateSchool($updateSchoolInput: UpdateSchoolInput!) {
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
    input AccountInput {
    id: ID
    Inst_UID: ID
    Institution_Name: String
    status: String
    NCES_LEAID:String
    SCHNO: String
    ... [other fields]
    }
    ```
​
- **Input Validation**:
​
  - **name**:
    - Type: String
    - Required: Yes
    - Validation: Max-length of 150 characters.
  - **status**:
    - Type: String
    - Required: Yes
    - Validation: Should be one of ["Active", "Inactive", "Pending"].
  - **email**:
    - Type: String
    - Validation: Must be a valid email format.
  - **phoneNumber**:
    - Type: String
    - Validation: Should be a valid phone number format.
  - **address**:
    - Type: String
    - Validation: Max-length of 500 characters.
​
- **Headers**:
​
  - `Authorization`: Bearer token for authentication.
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
      // Updated fields...
    }
  }
}

    ```
​
- **Error Handling**:
​
  - `400 Bad Request`: For validation errors, malformed GraphQL, or if required fields are missing.
  - `401 Unauthorized`: Invalid or missing token.
  - `409 Conflict`: If there's a conflict with existing data (e.g., duplicate account).
  - GraphQL-specific errors are encapsulated in the `errors` array in the response.
​
- **Deprecation Strategy**:
​
  - If there's any plan to deprecate fields or introduce breaking changes, these will be communicated in the API's changelog at least 3 months in advance.
  - Deprecated fields will be marked in the schema documentation and will still be available for 6 months post the deprecation announcement.
​
- **Sample Requests and Responses**:
​
  - **Request**:
​
    ```graphql
    mutation {
      UpdateSchool($updateSchoolInput: {Phone: "123456789", status: "Active", email: "account@example.com"}) {
        id
        name
        status
      }
    }
    ```
​
  - **Response**:
​
    ```json
    {
        "data": {
            "UpdateSchool": {
                "id": "12345",
                "Phone": "123456789",
                "status": "Active"
            }
        }
    }
    ```
​
- **Test Scenarios**
​
  - Update a School with valid data and authorization.
  - Attempt mutation without providing required fields.
  - Attempt mutation without authorization.
  - Test with an invalid token.
​
- **Test Cases**
​
  - **8.1 Update a School with valid data and authorization**
​
    - **Given**: User is authorized and sends a valid `Authorization` header with valid account data.
    - **When**: User sends a mutation request to update a school's information.
    - **Then**: Server responds with status `200 OK` and returns the updated school information.
​
  - **8.2 Attempt mutation without providing required fields**
​
    - **Given**: User is authorized but sends incomplete account data.
    - **When**: User sends a mutation request.
    - **Then**: Server responds with status `400 Bad Request`.
​
  - **8.3 Attempt mutation without authorization**
​
    - **Given**: User does not send an `Authorization` header but provides valid account data.
    - **When**: User sends a mutation request.
    - **Then**: Server responds with status `401 Unauthorized`.
​
  - **8.4 Test with an Invalid Token**
​
    - **Given**: User sends an invalid `Authorization` header.
    - **When**: User sends a mutation to update a school's information.
    - **Then**: Server responds with status `401 Unauthorized`.
