## GhostDocs API Documentation (Conceptual)

This document outlines the conceptual API for the GhostDocs AI Documentation Agent. As only the frontend `index.html` was provided, this API specification is based on the assumed functionality of an "AI Documentation Agent" and represents a typical design for such a system. The actual implementation details (e.g., specific programming language, framework) are placeholders.

### Base URL

`https://api.ghostdocs.com/v1` (Example)

### Authentication

API requests are expected to be authenticated using an API key or OAuth 2.0 token, passed in the `Authorization` header.

`Authorization: Bearer YOUR_API_KEY_OR_TOKEN`

### Error Handling

Errors are returned with appropriate HTTP status codes and a JSON body containing an `error` object with `code` and `message` fields.

```json
{
  "error": {
    "code": "INVALID_INPUT",
    "message": "The provided code snippet is empty or invalid."
  }
}
```

--- 

### 1. Generate Documentation

Generates comprehensive documentation for a given code snippet or project structure.

*   **Endpoint**: `/generate-docs`
*   **Method**: `POST`
*   **Description**: Submits code or project context to the AI agent for documentation generation. The response will include various documentation artifacts.

#### Request

*   **Headers**:
    *   `Content-Type: application/json`
    *   `Authorization: Bearer YOUR_API_KEY_OR_TOKEN`
*   **Body**:

    ```json
    {
      "project_name": "MyAwesomeProject",
      "code_context": [
        {
          "file_path": "src/main.py",
          "content": "def hello_world():\n    print('Hello, World!')"
        },
        {
          "file_path": "README.md",
          "content": "# My Awesome Project\nThis is a test project."
        }
      ],
      "output_formats": ["readme", "api_docs", "inline_comments", "mermaid_diagram"],
      "target_language": "python" // Optional: Hint for AI model
    }
    ```

    *   `project_name` (string, required): The name of the project being documented.
    *   `code_context` (array of objects, required): An array where each object represents a file in the codebase.
        *   `file_path` (string, required): The relative path of the file.
        *   `content` (string, required): The full content of the file.
    *   `output_formats` (array of strings, optional): A list of desired documentation formats. Defaults to all available formats if not specified. Possible values: `"readme"`, `"api_docs"`, `"inline_comments"`, `"mermaid_diagram"`.
    *   `target_language` (string, optional): A hint to the AI model about the primary programming language of the codebase.

#### Response

*   **Status**: `200 OK`
*   **Body**:

    ```json
    {
      "job_id": "doc_gen_12345",
      "status": "processing",
      "estimated_completion": "2023-10-27T10:30:00Z",
      "documentation_results": {
        "readme": "# My Awesome Project\n...",
        "api_docs": "## API Endpoints\n...",
        "inline_comments": [
          {
            "file_path": "src/main.py",
            "code_with_comments": "\"\"\"\nThis is a docstring for main.py\n\"\"\"\ndef hello_world():\n    \"\"\"Prints 'Hello, World!' to the console.\"\"\"\n    print('Hello, World!')"
          }
        ],
        "mermaid_diagram": "graph TD\n    A[User] --> B(Frontend)\n    B --> C{API Gateway}\n    C --> D[AI Service]"
      }
    }
    ```

    *   `job_id` (string): A unique identifier for the documentation generation job.
    *   `status` (string): The current status of the job (e.g., `"processing"`, `"completed"`, `"failed"`).
    *   `estimated_completion` (string, optional): An ISO 8601 timestamp indicating when the job is expected to complete.
    *   `documentation_results` (object, optional): An object containing the generated documentation artifacts. This field might be empty or partial if the job is still `"processing"` or `"failed"`.

#### Possible Error Codes

*   `400 Bad Request`: `INVALID_INPUT`, `MISSING_REQUIRED_FIELD`
*   `401 Unauthorized`: `AUTHENTICATION_FAILED`
*   `403 Forbidden`: `PERMISSION_DENIED`
*   `429 Too Many Requests`: `RATE_LIMIT_EXCEEDED`
*   `500 Internal Server Error`: `AI_PROCESSING_ERROR`, `DATABASE_ERROR`

--- 

### 2. Get Documentation Job Status

Retrieves the status and results of a previously submitted documentation generation job.

*   **Endpoint**: `/jobs/{job_id}`
*   **Method**: `GET`
*   **Description**: Allows clients to poll for the completion status and retrieve the final documentation output.

#### Request

*   **Headers**:
    *   `Authorization: Bearer YOUR_API_KEY_OR_TOKEN`
*   **Path Parameters**:
    *   `job_id` (string, required): The ID of the documentation job.

#### Response

*   **Status**: `200 OK`
*   **Body**:

    ```json
    {
      "job_id": "doc_gen_12345",
      "status": "completed",
      "started_at": "2023-10-27T10:00:00Z",
      "completed_at": "2023-10-27T10:25:00Z",
      "documentation_results": {
        "readme": "# My Awesome Project\n...",
        "api_docs": "## API Endpoints\n...",
        "inline_comments": [
          {
            "file_path": "src/main.py",
            "code_with_comments": "\"\"\"\nThis is a docstring for main.py\n\"\"\"\ndef hello_world():\n    \"\"\"Prints 'Hello, World!' to the console.\"\"\"\n    print('Hello, World!')"
          }
        ],
        "mermaid_diagram": "graph TD\n    A[User] --> B(Frontend)\n    B --> C{API Gateway}\n    C --> D[AI Service]"
      }
    }
    ```

    *   `job_id` (string): The unique identifier for the documentation generation job.
    *   `status` (string): The current status of the job (e.g., `"processing"`, `"completed"`, `"failed"`).
    *   `started_at` (string, optional): ISO 8601 timestamp when the job started.
    *   `completed_at` (string, optional): ISO 8601 timestamp when the job completed.
    *   `documentation_results` (object, optional): The generated documentation artifacts. This will be present and complete if `status` is `"completed"`.

#### Possible Error Codes

*   `401 Unauthorized`: `AUTHENTICATION_FAILED`
*   `403 Forbidden`: `PERMISSION_DENIED`
*   `404 Not Found`: `JOB_NOT_FOUND`
*   `500 Internal Server Error`: `DATABASE_ERROR`
