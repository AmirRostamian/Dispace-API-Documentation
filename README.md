# Dispace API Documentation

## Overview

Dispace is an AI-powered Discovery-as-a-Service platform that combines an intelligent AI Copilot with human expertise to guide founders, students, and product teams through structured, personalized discovery journeys. From breaking down raw ideas into hypotheses and identifying potential risks, to generating tailored research tasks and connecting users with expert feedback, Dispace helps innovators move faster, think critically, and build with confidence—turning uncertainty into actionable insights.

### Core Functionalities

- **Discovery Flow**: Create and manage discovery spaces where teams can define objectives, create tasks, and submit results to validate ideas and hypotheses.
- **Collaboration Matching**: Connect with potential collaborators based on skills, interests, and project needs through an intelligent matching system.

### Base URL

```
https://api.dispace.tech/v1
```

---

## Authentication

Dispace uses JWT (JSON Web Tokens) for authentication. Include your JWT token in the `Authorization` header for all authenticated requests.

### Obtaining a Token

**Endpoint**: `POST /auth/login`

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "your_secure_password"
}
```

**Response**:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "usr_abc123",
    "email": "user@example.com",
    "name": "John Doe"
  },
  "expiresIn": 86400
}
```

### Using the Token

Include the token in all subsequent requests:

```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Discovery Flow API

### Spaces

#### Create a Discovery Space

**Endpoint**: `POST /discovery/spaces`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "name": "Market Validation for AI SaaS",
  "description": "Validate market demand for our AI-powered analytics tool",
  "objective": "Determine if enterprise customers will pay for automated data insights",
  "startDate": "2025-10-15",
  "endDate": "2025-12-15",
  "tags": ["market-research", "b2b", "saas"]
}
```

**Response**: `201 Created`
```json
{
  "id": "spc_xyz789",
  "name": "Market Validation for AI SaaS",
  "description": "Validate market demand for our AI-powered analytics tool",
  "objective": "Determine if enterprise customers will pay for automated data insights",
  "status": "active",
  "startDate": "2025-10-15T00:00:00Z",
  "endDate": "2025-12-15T00:00:00Z",
  "tags": ["market-research", "b2b", "saas"],
  "ownerId": "usr_abc123",
  "createdAt": "2025-10-07T10:30:00Z",
  "updatedAt": "2025-10-07T10:30:00Z"
}
```

#### List Discovery Spaces

**Endpoint**: `GET /discovery/spaces`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
```

**Query Parameters**:
- `status` (optional): Filter by status (`active`, `completed`, `archived`)
- `page` (optional): Page number (default: 1)
- `limit` (optional): Results per page (default: 20, max: 100)

**Example Request**:
```bash
curl -X GET "https://api.dispace.io/v1/discovery/spaces?status=active&page=1&limit=10" \
  -H "Authorization: Bearer {your_jwt_token}"
```

**Response**: `200 OK`
```json
{
  "spaces": [
    {
      "id": "spc_xyz789",
      "name": "Market Validation for AI SaaS",
      "description": "Validate market demand for our AI-powered analytics tool",
      "status": "active",
      "startDate": "2025-10-15T00:00:00Z",
      "endDate": "2025-12-15T00:00:00Z",
      "taskCount": 5,
      "completedTasks": 2,
      "createdAt": "2025-10-07T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 1,
    "totalPages": 1
  }
}
```

#### Get a Specific Space

**Endpoint**: `GET /discovery/spaces/{spaceId}`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
```

**Response**: `200 OK`
```json
{
  "id": "spc_xyz789",
  "name": "Market Validation for AI SaaS",
  "description": "Validate market demand for our AI-powered analytics tool",
  "objective": "Determine if enterprise customers will pay for automated data insights",
  "status": "active",
  "startDate": "2025-10-15T00:00:00Z",
  "endDate": "2025-12-15T00:00:00Z",
  "tags": ["market-research", "b2b", "saas"],
  "ownerId": "usr_abc123",
  "members": [
    {
      "userId": "usr_abc123",
      "role": "owner",
      "joinedAt": "2025-10-07T10:30:00Z"
    }
  ],
  "createdAt": "2025-10-07T10:30:00Z",
  "updatedAt": "2025-10-07T10:30:00Z"
}
```

---

### Tasks

#### Create a Task

**Endpoint**: `POST /discovery/spaces/{spaceId}/tasks`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "title": "Conduct 10 Customer Interviews",
  "description": "Interview potential enterprise customers to understand their analytics needs",
  "type": "research",
  "priority": "high",
  "assignedTo": "usr_abc123",
  "dueDate": "2025-10-25",
  "deliverables": [
    "Interview transcripts",
    "Summary of key findings",
    "List of pain points identified"
  ]
}
```

**Response**: `201 Created`
```json
{
  "id": "tsk_def456",
  "spaceId": "spc_xyz789",
  "title": "Conduct 10 Customer Interviews",
  "description": "Interview potential enterprise customers to understand their analytics needs",
  "type": "research",
  "status": "todo",
  "priority": "high",
  "assignedTo": "usr_abc123",
  "dueDate": "2025-10-25T00:00:00Z",
  "deliverables": [
    "Interview transcripts",
    "Summary of key findings",
    "List of pain points identified"
  ],
  "createdAt": "2025-10-07T11:00:00Z",
  "updatedAt": "2025-10-07T11:00:00Z"
}
```

#### List Tasks in a Space

**Endpoint**: `GET /discovery/spaces/{spaceId}/tasks`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
```

**Query Parameters**:
- `status` (optional): Filter by status (`todo`, `in_progress`, `completed`)
- `assignedTo` (optional): Filter by assigned user ID
- `priority` (optional): Filter by priority (`low`, `medium`, `high`)

**Response**: `200 OK`
```json
{
  "tasks": [
    {
      "id": "tsk_def456",
      "spaceId": "spc_xyz789",
      "title": "Conduct 10 Customer Interviews",
      "type": "research",
      "status": "in_progress",
      "priority": "high",
      "assignedTo": "usr_abc123",
      "dueDate": "2025-10-25T00:00:00Z",
      "createdAt": "2025-10-07T11:00:00Z"
    }
  ],
  "total": 1
}
```

---

### Task Results

#### Submit Task Result

**Endpoint**: `POST /discovery/spaces/{spaceId}/tasks/{taskId}/results`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "summary": "Completed 10 interviews with enterprise decision-makers",
  "findings": [
    "85% struggle with manual data analysis",
    "Average time spent on reports: 15 hours/week",
    "Willingness to pay: $500-2000/month"
  ],
  "attachments": [
    {
      "name": "interview_transcripts.pdf",
      "url": "https://storage.dispace.io/files/abc123.pdf",
      "type": "document"
    }
  ],
  "metrics": {
    "interviewsCompleted": 10,
    "averageInterviewDuration": 45,
    "satisfactionScore": 8.5
  }
}
```

**Response**: `201 Created`
```json
{
  "id": "res_ghi789",
  "taskId": "tsk_def456",
  "spaceId": "spc_xyz789",
  "submittedBy": "usr_abc123",
  "summary": "Completed 10 interviews with enterprise decision-makers",
  "findings": [
    "85% struggle with manual data analysis",
    "Average time spent on reports: 15 hours/week",
    "Willingness to pay: $500-2000/month"
  ],
  "attachments": [
    {
      "name": "interview_transcripts.pdf",
      "url": "https://storage.dispace.io/files/abc123.pdf",
      "type": "document"
    }
  ],
  "metrics": {
    "interviewsCompleted": 10,
    "averageInterviewDuration": 45,
    "satisfactionScore": 8.5
  },
  "status": "submitted",
  "submittedAt": "2025-10-26T14:30:00Z",
  "createdAt": "2025-10-26T14:30:00Z"
}
```

#### List Task Results

**Endpoint**: `GET /discovery/spaces/{spaceId}/tasks/{taskId}/results`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
```

**Response**: `200 OK`
```json
{
  "results": [
    {
      "id": "res_ghi789",
      "taskId": "tsk_def456",
      "submittedBy": "usr_abc123",
      "summary": "Completed 10 interviews with enterprise decision-makers",
      "status": "submitted",
      "submittedAt": "2025-10-26T14:30:00Z"
    }
  ],
  "total": 1
}
```

---

## Collaboration Matching API

### Find Collaborators

#### Get Potential Matches

**Endpoint**: `POST /collaboration/matches/search`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "skills": ["frontend-development", "ui-ux-design", "react"],
  "role": "technical-cofounder",
  "commitment": "full-time",
  "location": "remote",
  "experience": "senior",
  "interests": ["saas", "b2b", "ai-ml"]
}
```

**Response**: `200 OK`
```json
{
  "matches": [
    {
      "id": "mch_jkl012",
      "user": {
        "id": "usr_xyz456",
        "name": "Jane Smith",
        "avatar": "https://storage.dispace.io/avatars/xyz456.jpg",
        "title": "Senior Full-Stack Developer",
        "location": "Remote"
      },
      "matchScore": 92,
      "skills": ["react", "typescript", "ui-ux-design", "nodejs"],
      "experience": "senior",
      "availability": "full-time",
      "interests": ["saas", "b2b", "fintech"],
      "commonConnections": 3,
      "reason": "Strong technical skills match with your requirements, shared interest in B2B SaaS"
    }
  ],
  "total": 1
}
```

#### Send Collaboration Request

**Endpoint**: `POST /collaboration/matches`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "recipientId": "usr_xyz456",
  "spaceId": "spc_xyz789",
  "role": "technical-cofounder",
  "message": "Hi Jane, I came across your profile and think your frontend expertise would be perfect for our AI analytics platform. Would love to discuss potential collaboration!",
  "proposedEquity": 15,
  "proposedCommitment": "full-time"
}
```

**Response**: `201 Created`
```json
{
  "id": "col_mno345",
  "senderId": "usr_abc123",
  "recipientId": "usr_xyz456",
  "spaceId": "spc_xyz789",
  "role": "technical-cofounder",
  "message": "Hi Jane, I came across your profile and think your frontend expertise would be perfect for our AI analytics platform. Would love to discuss potential collaboration!",
  "proposedEquity": 15,
  "proposedCommitment": "full-time",
  "status": "pending",
  "sentAt": "2025-10-07T15:00:00Z",
  "createdAt": "2025-10-07T15:00:00Z"
}
```

---

### Manage Collaboration Requests

#### List Received Requests

**Endpoint**: `GET /collaboration/matches/received`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
```

**Query Parameters**:
- `status` (optional): Filter by status (`pending`, `accepted`, `rejected`)

**Response**: `200 OK`
```json
{
  "requests": [
    {
      "id": "col_mno345",
      "sender": {
        "id": "usr_abc123",
        "name": "John Doe",
        "avatar": "https://storage.dispace.io/avatars/abc123.jpg",
        "title": "Product Manager & Founder"
      },
      "space": {
        "id": "spc_xyz789",
        "name": "Market Validation for AI SaaS"
      },
      "role": "technical-cofounder",
      "message": "Hi Jane, I came across your profile...",
      "proposedEquity": 15,
      "proposedCommitment": "full-time",
      "status": "pending",
      "sentAt": "2025-10-07T15:00:00Z"
    }
  ],
  "total": 1
}
```

#### Accept Collaboration Request

**Endpoint**: `POST /collaboration/matches/{matchId}/accept`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "message": "Excited to collaborate! Let's schedule a call to discuss next steps.",
  "counterOffer": {
    "equity": 20,
    "commitment": "full-time"
  }
}
```

**Response**: `200 OK`
```json
{
  "id": "col_mno345",
  "senderId": "usr_abc123",
  "recipientId": "usr_xyz456",
  "spaceId": "spc_xyz789",
  "role": "technical-cofounder",
  "status": "accepted",
  "acceptedAt": "2025-10-08T09:00:00Z",
  "responseMessage": "Excited to collaborate! Let's schedule a call to discuss next steps.",
  "counterOffer": {
    "equity": 20,
    "commitment": "full-time"
  },
  "nextSteps": {
    "meetingScheduled": false,
    "contractSent": false
  }
}
```

#### Reject Collaboration Request

**Endpoint**: `POST /collaboration/matches/{matchId}/reject`

**Headers**:
```
Authorization: Bearer {your_jwt_token}
Content-Type: application/json
```

**Request Body**:
```json
{
  "message": "Thank you for reaching out. Unfortunately, I'm committed to another project at the moment.",
  "reason": "timing"
}
```

**Response**: `200 OK`
```json
{
  "id": "col_mno345",
  "status": "rejected",
  "rejectedAt": "2025-10-08T09:15:00Z",
  "responseMessage": "Thank you for reaching out. Unfortunately, I'm committed to another project at the moment.",
  "reason": "timing"
}
```

---

## Data Schemas

### Space Object

```json
{
  "id": "string",
  "name": "string",
  "description": "string",
  "objective": "string",
  "status": "active | completed | archived",
  "startDate": "ISO 8601 datetime",
  "endDate": "ISO 8601 datetime",
  "tags": ["string"],
  "ownerId": "string",
  "members": [
    {
      "userId": "string",
      "role": "owner | member | viewer",
      "joinedAt": "ISO 8601 datetime"
    }
  ],
  "createdAt": "ISO 8601 datetime",
  "updatedAt": "ISO 8601 datetime"
}
```

### Task Object

```json
{
  "id": "string",
  "spaceId": "string",
  "title": "string",
  "description": "string",
  "type": "research | experiment | analysis | design | development",
  "status": "todo | in_progress | completed | blocked",
  "priority": "low | medium | high",
  "assignedTo": "string",
  "dueDate": "ISO 8601 datetime",
  "deliverables": ["string"],
  "createdAt": "ISO 8601 datetime",
  "updatedAt": "ISO 8601 datetime"
}
```

### Result Object

```json
{
  "id": "string",
  "taskId": "string",
  "spaceId": "string",
  "submittedBy": "string",
  "summary": "string",
  "findings": ["string"],
  "attachments": [
    {
      "name": "string",
      "url": "string",
      "type": "document | image | video | other"
    }
  ],
  "metrics": {
    "key": "value"
  },
  "status": "submitted | approved | revision_requested",
  "submittedAt": "ISO 8601 datetime",
  "createdAt": "ISO 8601 datetime"
}
```

### Match Object

```json
{
  "id": "string",
  "senderId": "string",
  "recipientId": "string",
  "spaceId": "string",
  "role": "string",
  "message": "string",
  "proposedEquity": "number",
  "proposedCommitment": "full-time | part-time | contract",
  "status": "pending | accepted | rejected | expired",
  "sentAt": "ISO 8601 datetime",
  "respondedAt": "ISO 8601 datetime",
  "createdAt": "ISO 8601 datetime"
}
```

---

## Integration Flow

Here's a typical integration workflow for using Dispace:

### 1. Authentication
```bash
# Login to get JWT token
curl -X POST https://api.dispace.io/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password123"}'
```

### 2. Create a Discovery Space
```bash
# Create a new space for your project
curl -X POST https://api.dispace.io/v1/discovery/spaces \
  -H "Authorization: Bearer {your_jwt_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Market Validation",
    "description": "Validate our product idea",
    "objective": "Confirm market demand",
    "startDate": "2025-10-15",
    "endDate": "2025-12-15"
  }'
```

### 3. Add Tasks to the Space
```bash
# Create tasks within your space
curl -X POST https://api.dispace.io/v1/discovery/spaces/spc_xyz789/tasks \
  -H "Authorization: Bearer {your_jwt_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Customer Interviews",
    "description": "Interview 10 potential customers",
    "type": "research",
    "priority": "high",
    "dueDate": "2025-10-25"
  }'
```

### 4. Submit Task Results
```bash
# Submit results when tasks are completed
curl -X POST https://api.dispace.io/v1/discovery/spaces/spc_xyz789/tasks/tsk_def456/results \
  -H "Authorization: Bearer {your_jwt_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "summary": "Completed interviews",
    "findings": ["Key insight 1", "Key insight 2"]
  }'
```

### 5. Find Collaborators
```bash
# Search for potential collaborators
curl -X POST https://api.dispace.io/v1/collaboration/matches/search \
  -H "Authorization: Bearer {your_jwt_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "skills": ["frontend-development"],
    "role": "technical-cofounder",
    "commitment": "full-time"
  }'
```

### 6. Send Collaboration Request
```bash
# Reach out to a potential collaborator
curl -X POST https://api.dispace.io/v1/collaboration/matches \
  -H "Authorization: Bearer {your_jwt_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "recipientId": "usr_xyz456",
    "spaceId": "spc_xyz789",
    "role": "technical-cofounder",
    "message": "Would love to collaborate!"
  }'
```

---

## Error Handling

All error responses follow this format:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": {
      "field": "Additional context about the error"
    }
  }
}
```

### Common Error Codes

| Status Code | Error Code | Description |
|-------------|------------|-------------|
| 400 | `INVALID_REQUEST` | Request body or parameters are invalid |
| 401 | `UNAUTHORIZED` | Missing or invalid authentication token |
| 403 | `FORBIDDEN` | User lacks permission for this action |
| 404 | `NOT_FOUND` | Requested resource does not exist |
| 409 | `CONFLICT` | Request conflicts with existing resource |
| 429 | `RATE_LIMIT_EXCEEDED` | Too many requests, please slow down |
| 500 | `INTERNAL_ERROR` | Server error, please try again |

---

## Rate Limits

API rate limits are enforced per user account:

- **Free tier**: 100 requests/hour
- **Pro tier**: 1,000 requests/hour
- **Enterprise tier**: 10,000 requests/hour

Rate limit information is included in response headers:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 995
X-RateLimit-Reset: 1696777200
```

---

## Support

For questions, bug reports, or feature requests:

- **Support Email**: support@dispace.io
- **GitHub Issues**: https://github.com/dispace/api/issues

---

## Changelog

### v1.0.0 (October 2025)
- Initial API release
- Discovery Flow endpoints
- Collaboration Matching endpoints
- JWT authentication

---

**License**: MIT

**© 2025 Dispace. All rights reserved.**
