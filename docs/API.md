## GhostDocs API Documentation

This document outlines the RESTful API endpoints for the GhostDocs AI Documentation Agent. The API facilitates interaction with the AI core for documentation generation, code analysis, and project management.

**Note:** *The following API documentation is speculative and based on the assumed functionality of an "AI Documentation Agent" due to the limited codebase context provided. Actual endpoints and data structures may vary.*

--- 

### Base URL

`https://api.ghostdocs.com/v1` (or `http://localhost:PORT/api/v1` for local development)

### Authentication

API requests are secured using **Bearer Token Authentication**. Include your API key in the `Authorization` header for all protected endpoints:

`Authorization: Bearer YOUR_API_KEY`

--- 

### 1. Project Management

#### `POST /projects`

Creates a new documentation project.

*   **Description**: Initializes a new project, optionally linking it to a source code repository or accepting an initial code upload.
*   **Request Body**: `application/json`
    ```json
    {
      "name": "My New Project",
      "description": "Documentation for a new microservice.",
      "repositoryUrl": "https://github.com/user/repo",
      "initialCodebase": "base64_encoded_zip_or_tar_of_code" // Optional
    }
    ```
*   **Responses**:
    *   `201 Created`: Project successfully created.
        ```json
        {
          "id": "proj_abc123",
          "name": "My New Project",
          "status": "initialized",
          "createdAt": "2023-10-27T10:00:00Z"
        }
        ```
    *   `400 Bad Request`: Invalid input.
    *   `401 Unauthorized`: Missing or invalid authentication token.

#### `GET /projects/{projectId}`

Retrieves details for a specific project.

*   **Description**: Fetches all metadata and current status for a given project ID.
*   **Path Parameters**:
    *   `projectId` (string, required): The unique identifier of the project.
*   **Responses**:
    *   `200 OK`: Project details retrieved.
        ```json
        {
          "id": "proj_abc123",
          "name": "My New Project",
          "description": "Documentation for a new microservice.",
          "repositoryUrl": "https://github.com/user/repo",
          "status": "ready_for_generation",
          "lastGeneratedAt": "2023-10-27T11:30:00Z"
        }
        ```
    *   `404 Not Found`: Project not found.
    *   `401 Unauthorized`: Missing or invalid authentication token.

--- 

### 2. Documentation Generation

#### `POST /projects/{projectId}/generate`

Triggers documentation generation for a project.

*   **Description**: Initiates the AI documentation generation process for the specified project. This can be a long-running operation.
*   **Path Parameters**:
    *   `projectId` (string, required): The unique identifier of the project.
*   **Request Body**: `application/json` (Optional, to specify types of docs to generate)
    ```json
    {
      "docTypes": ["readme", "api_docs", "inline_comments", "mermaid_diagram"],
      "forceRegenerate": false
    }
    ```
*   **Responses**:
    *   `202 Accepted`: Documentation generation process started.
        ```json
        {
          "message": "Documentation generation initiated.",
          "jobId": "job_xyz789",
          "statusUrl": "/api/v1/jobs/job_xyz789"
        }
        ```
    *   `404 Not Found`: Project not found.
    *   `401 Unauthorized`: Missing or invalid authentication token.

#### `GET /jobs/{jobId}`

Checks the status of a documentation generation job.

*   **Description**: Retrieves the current status and progress of an asynchronous documentation generation job.
*   **Path Parameters**:
    *   `jobId` (string, required): The unique identifier of the generation job.
*   **Responses**:
    *   `200 OK`: Job status retrieved.
        ```json
        {
          "id": "job_xyz789",
          "projectId": "proj_abc123",
          "status": "processing", // or "completed", "failed", "pending"
          "progress": 75, // Percentage
          "resultUrl": "/api/v1/projects/proj_abc123/docs" // Available if status is "completed"
        }
        ```
    *   `404 Not Found`: Job not found.
    *   `401 Unauthorized`: Missing or invalid authentication token.

#### `GET /projects/{projectId}/docs`

Retrieves the generated documentation for a project.

*   **Description**: Fetches the latest generated documentation for a project. This endpoint returns a structured JSON object containing all generated documentation types.
*   **Path Parameters**:
    *   `projectId` (string, required): The unique identifier of the project.
*   **Responses**:
    *   `200 OK`: Documentation retrieved.
        ```json
        {
          "projectId": "proj_abc123",
          "generatedAt": "2023-10-27T12:00:00Z",
          "readme": "# My Project README...",
          "api_docs": "## API Endpoints...",
          "inline_comments": [
            { "file_path": "src/index.js", "code_with_comments": "// JSDoc..." }
          ],
          "mermaid_diagram": "graph TD\n  A[Start] --> B[End]"
        }
        ```
    *   `404 Not Found`: Project or documentation not found.
    *   `401 Unauthorized`: Missing or invalid authentication token.

--- 

### 3. Codebase Management

#### `POST /projects/{projectId}/upload-code`

Uploads a new version of the codebase for analysis.

*   **Description**: Allows users to upload a new or updated codebase (e.g., as a ZIP archive) for the AI agent to analyze and generate documentation from.
*   **Path Parameters**:
    *   `projectId` (string, required): The unique identifier of the project.
*   **Request Body**: `multipart/form-data`
    *   `file` (file, required): The codebase archive (e.g., `.zip`, `.tar.gz`).
*   **Responses**:
    *   `200 OK`: Codebase uploaded and processing initiated.
        ```json
        {
          "message": "Codebase uploaded successfully. Analysis initiated.",
          "analysisJobId": "analysis_def456"
        }
        ```
    *   `400 Bad Request`: Invalid file type or no file provided.
    *   `401 Unauthorized`: Missing or invalid authentication token.

--- 

### Error Handling

All error responses follow a standard format:

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "Invalid input provided for project name."
}
```

Common error codes:

*   `400 Bad Request`: Client-side error, e.g., invalid request body, missing parameters.
*   `401 Unauthorized`: Authentication failed or token is missing/invalid.
*   `403 Forbidden`: Authenticated but not authorized to perform the action.
*   `404 Not Found`: The requested resource does not exist.
*   `500 Internal Server Error`: An unexpected error occurred on the server.
*   `503 Service Unavailable`: The server is temporarily unable to handle the request.
