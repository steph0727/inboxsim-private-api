---
layout: default
---

# Inbox Sim - Private API Documentation

## Overview

The Inbox Sim Private API supports internal faculty content management, assessment evaluation, scenario branching, and application administration. These services are intended for authorized internal application components rather than third-party client applications.

This documentation describes proposed endpoints, HTTP methods, inputs, outputs, and example requests.

### API Categories

- **Faculty CMS:** Create, edit, and delete simulated inbox scenarios and messages.
- **Assessment Management:** Evaluate student responses and manage assessment criteria.
- **Scenario Logic:** Maintain the branching paths that determine which simulated messages appear next.
- **Administration:** Manage internal roles and permissions.

### Authentication and Authorization

All private endpoints require authentication and explicit role-based authorization. Faculty members may manage only projects they are assigned to; administrative operations require an administrator role.

The server must determine the user's identity from validated credentials, not from an untrusted user ID supplied in a request. The specific authentication method must be confirmed by the development team.

The documentation may be publicly hosted for this assignment, but publishing these descriptions does **not** make the private services publicly accessible.

### General Conventions

- HTTP methods describe the operation being performed.
- Integer values identify projects, scenarios, messages, responses, and users.
- String values contain text.
- Boolean values indicate true or false.
- Arrays contain collections of items.
- Objects contain related fields.
- Example IDs and values in this document are fictional.

---

# 2. Faculty CMS Endpoints

## 2.1 Create Scenario

**Endpoint:** `/internal/scenarios/create/`

**Method:** `POST`

### Inputs

| Name        | Data Type | Description                                       |
| ----------- | --------- | ------------------------------------------------- |
| project_id  | Integer   | Identifier of the project containing the scenario |
| title       | String    | Name of the new scenario                          |
| description | String    | Summary of the scenario's purpose                 |

### Outputs

| Name        | Data Type | Description                                    |
| ----------- | --------- | ---------------------------------------------- |
| success     | Boolean   | Indicates whether the scenario was created     |
| scenario_id | Integer   | Unique identifier assigned to the new scenario |
| result      | String    | Confirmation or error description              |

### Example Usage

`POST /internal/scenarios/create/`

Example request body:

```json
{
  "project_id": 10,
  "title": "Customer Service Simulation",
  "description": "Students respond to a sequence of workplace emails."
}
```

### Example Response

```json
{
  "success": true,
  "scenario_id": 72,
  "result": "Scenario created successfully."
}
```

---

## 2.2 Update Scenario

**Endpoint:** `/internal/scenarios/update/`

**Method:** `PATCH`

### Inputs

| Name        | Data Type | Description                              |
| ----------- | --------- | ---------------------------------------- |
| scenario_id | Integer   | Identifier of the scenario being edited  |
| title       | String    | Updated scenario title, if changed       |
| description | String    | Updated scenario description, if changed |

### Outputs

| Name        | Data Type | Description                            |
| ----------- | --------- | -------------------------------------- |
| success     | Boolean   | Indicates whether the update succeeded |
| scenario_id | Integer   | Identifier of the updated scenario     |
| result      | String    | Confirmation or error description      |

### Example Usage

`PATCH /internal/scenarios/update/`

Example request body:

```json
{
  "scenario_id": 72,
  "title": "Customer Service and Escalation Simulation"
}
```

### Example Response

```json
{
  "success": true,
  "scenario_id": 72,
  "result": "Scenario updated successfully."
}
```

---

## 2.3 Delete Scenario

**Endpoint:** `/internal/scenarios/delete/`

**Method:** `DELETE`

### Inputs

| Name        | Data Type | Description                                 |
| ----------- | --------- | ------------------------------------------- |
| scenario_id | Integer   | Unique identifier of the scenario to delete |

### Outputs

| Name    | Data Type | Description                          |
| ------- | --------- | ------------------------------------ |
| success | Boolean   | Indicates whether deletion succeeded |
| result  | String    | Confirmation or error description    |

### Example Usage

`DELETE /internal/scenarios/delete/?scenario_id=72`

### Example Response

```json
{
  "success": true,
  "result": "Draft scenario deleted successfully."
}
```

---

## 2.4 Create Scenario Message

**Endpoint:** `/internal/scenario/messages/create/`

**Method:** `POST`

### Inputs

| Name        | Data Type | Description                                       |
| ----------- | --------- | ------------------------------------------------- |
| scenario_id | Integer   | Identifier of the scenario containing the message |
| subject     | String    | Subject of the simulated email                    |
| sender      | String    | Display name of the simulated sender              |
| body        | String    | Content of the simulated email                    |

### Outputs

| Name       | Data Type | Description                               |
| ---------- | --------- | ----------------------------------------- |
| success    | Boolean   | Indicates whether the message was created |
| message_id | Integer   | Identifier assigned to the new message    |
| result     | String    | Confirmation or error description         |

### Example Usage

`POST /internal/scenario/messages/create/`

Example request body:

```json
{
  "scenario_id": 72,
  "subject": "Customer Complaint",
  "sender": "Customer Support",
  "body": "A customer has reported an issue with their order."
}
```

### Example Response

```json
{
  "success": true,
  "message_id": 45,
  "result": "Message created successfully."
}
```

---

# 3. Assessment Management Endpoints

## 3.1 Create Assessment Rubric

**Endpoint:** `/internal/assessments/rubrics/create/`

**Method:** `POST`

### Inputs

| Name        | Data Type        | Description                                 |
| ----------- | ---------------- | ------------------------------------------- |
| project_id  | Integer          | Identifier of the assessment project        |
| rubric_name | String           | Name of the evaluation rubric               |
| criteria    | Array of Objects | Scoring categories and maximum point values |

Each criterion object contains `name` (String) and `max_points` (Number).

### Outputs

| Name      | Data Type | Description                              |
| --------- | --------- | ---------------------------------------- |
| success   | Boolean   | Indicates whether the rubric was created |
| rubric_id | Integer   | Unique identifier of the rubric          |
| result    | String    | Confirmation or error description        |

### Example Usage

`POST /internal/assessments/rubrics/create/`

Example request body:

```json
{
  "project_id": 10,
  "rubric_name": "Customer Service Rubric",
  "criteria": [
    { "name": "Professionalism", "max_points": 40 },
    { "name": "Problem Solving", "max_points": 60 }
  ]
}
```

### Example Response

```json
{
  "success": true,
  "rubric_id": 8,
  "result": "Rubric created successfully."
}
```

---

## 3.2 Grade Student Response

**Endpoint:** `/internal/assessments/grade_response/`

**Method:** `POST`

### Inputs

| Name        | Data Type | Description                                     |
| ----------- | --------- | ----------------------------------------------- |
| response_id | Integer   | Identifier of the submitted student response    |
| rubric_id   | Integer   | Identifier of the rubric used for evaluation    |
| score       | Number    | Assigned score, within the rubric's valid range |
| feedback    | String    | Faculty comments explaining the score           |

### Outputs

| Name        | Data Type | Description                         |
| ----------- | --------- | ----------------------------------- |
| success     | Boolean   | Indicates whether grading was saved |
| response_id | Integer   | Identifier of the graded response   |
| result      | String    | Confirmation or error description   |

### Example Usage

`POST /internal/assessments/grade_response/`

Example request body:

```json
{
  "response_id": 501,
  "rubric_id": 8,
  "score": 85,
  "feedback": "Good customer service approach with room for improvement."
}
```

### Example Response

```json
{
  "success": true,
  "response_id": 501,
  "result": "Assessment grade saved successfully."
}
```

---

# 4. Administration Endpoints

## 4.1 Update User Role

**Endpoint:** `/internal/admin/users/role/`

**Method:** `PATCH`

### Inputs

| Name    | Data Type | Description                                          |
| ------- | --------- | ---------------------------------------------------- |
| user_id | Integer   | Identifier of the account to update                  |
| role    | String    | New application role, such as `student` or `faculty` |

### Outputs

| Name    | Data Type | Description                            |
| ------- | --------- | -------------------------------------- |
| success | Boolean   | Indicates whether the role was changed |
| user_id | Integer   | Identifier of the affected user        |
| role    | String    | User's updated role                    |
| result  | String    | Confirmation or error description      |

### Example Usage

`PATCH /internal/admin/users/role/`

Example request body:

```json
{
  "user_id": 123,
  "role": "faculty"
}
```

### Example Response

```json
{
  "success": true,
  "user_id": 123,
  "role": "faculty",
  "result": "User role updated successfully."
}
```

---

# 5. General Error Handling

The private API should return appropriate HTTP status codes and error information. Exact response formats should be confirmed by the development team.

| HTTP Status               | Meaning                                                       |
| ------------------------- | ------------------------------------------------------------- |
| 200 OK                    | Request completed successfully                                |
| 201 Created               | Resource created successfully                                 |
| 400 Bad Request           | Request contains invalid input                                |
| 401 Unauthorized          | Authentication is missing or invalid                          |
| 403 Forbidden             | Requester lacks permission for this internal operation        |
| 404 Not Found             | Requested resource does not exist                             |
| 409 Conflict              | Operation conflicts with existing state or assessment records |
| 500 Internal Server Error | Unexpected server error occurred                              |

### Example Error Response

```json
{
  "success": false,
  "error": "You do not have permission to modify this scenario."
}
```
