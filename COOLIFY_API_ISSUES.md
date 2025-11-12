# Coolify API Issues Documentation

## Summary
Attempted to create a static HTML application in Coolify using the MCP Coolify tools, but encountered API errors when trying to create the application programmatically.

## What Was Attempted

### 1. Initial Setup (Successful)
- ✅ Listed available servers: Found `localhost` server (UUID: `ewww4cko8gw8sc40ks0k400w`)
- ✅ Listed available projects: Found `Test_Project` (UUID: `ks4cgko444kw8sso08w8k8c8`)
- ✅ Listed environments: Found `production` environment (UUID: `q0oc84soc8o84cc44ckkkswg`)
- ✅ Created GitHub repository: `https://github.com/systemnova/help-docs-platform.git`
- ✅ Created HTML file and Dockerfile
- ✅ Pushed code to GitHub repository

### 2. Application Creation Attempts (Failed)

#### Attempt 1: Using environment_name
**Request Payload:**
```json
{
  "project_uuid": "ks4cgko444kw8sso08w8k8c8",
  "environment_name": "production",
  "destination_uuid": "ewww4cko8gw8sc40ks0k400w",
  "ports_exposes": "80",
  "git_repository": "https://github.com/systemnova/help-docs-platform.git"
}
```

**Complete Error Response:**
```json
{
  "error": "MCP error -32603: MCP error -32603: Coolify API error: Not found."
}
```

**Error Details:**
- Error Code: `-32603` (Internal JSON-RPC error)
- Error Type: `MCP error` (wrapped twice)
- Coolify API Error: `Not found`
- HTTP Status: Not provided in response
- Response Body: Not provided in response

#### Attempt 2: Using environment_uuid instead
**Request Payload:**
```json
{
  "project_uuid": "ks4cgko444kw8sso08w8k8c8",
  "environment_uuid": "q0oc84soc8o84cc44ckkkswg",
  "destination_uuid": "ewww4cko8gw8sc40ks0k400w",
  "ports_exposes": "80",
  "git_repository": "https://github.com/systemnova/help-docs-platform.git"
}
```

**Complete Error Response:**
```
Error calling tool: Required parameter 'environment_name' is missing for tool create_application
```

**Error Details:**
- Error Type: Tool validation error (pre-API call)
- Issue: Tool definition requires `environment_name` parameter
- Note: This error occurs before the API call is made

#### Attempt 3: Using both environment_name and environment_uuid
**Request Payload:**
```json
{
  "project_uuid": "ks4cgko444kw8sso08w8k8c8",
  "environment_name": "production",
  "environment_uuid": "q0oc84soc8o84cc44ckkkswg",
  "destination_uuid": "ewww4cko8gw8sc40ks0k400w",
  "ports_exposes": "80",
  "git_repository": "https://github.com/systemnova/help-docs-platform.git"
}
```

**Complete Error Response:**
```json
{
  "error": "MCP error -32603: MCP error -32603: Coolify API error: Not found."
}
```

**Error Details:**
- Error Code: `-32603` (Internal JSON-RPC error)
- Error Type: `MCP error` (wrapped twice)
- Coolify API Error: `Not found`
- HTTP Status: Not provided in response
- Response Body: Not provided in response

## Successful API Responses (For Reference)

### list_servers Response
```json
[
  {
    "uuid": "ewww4cko8gw8sc40ks0k400w",
    "description": "This is the server where Coolify is running on. Don't delete this!",
    "name": "localhost",
    "ip": "host.docker.internal",
    "is_coolify_host": true,
    "is_reachable": true,
    "is_usable": true,
    "port": 22,
    "user": "root"
  }
]
```

### list_projects Response
```json
[
  {
    "id": 3,
    "uuid": "uow0cgkskg0k4wook4oowkkw",
    "name": "Postgres",
    "description": ""
  },
  {
    "id": 22,
    "uuid": "ks4cgko444kw8sso08w8k8c8",
    "description": "This is a test project",
    "name": "Test_Project"
  }
]
```

### list_environments Response
```json
[
  {
    "id": 39,
    "uuid": "q0oc84soc8o84cc44ckkkswg",
    "name": "production"
  }
]
```

### get_project Response (Successful)
```json
{
  "id": 22,
  "uuid": "ks4cgko444kw8sso08w8k8c8",
  "description": "This is a test project",
  "name": "Test_Project",
  "environments": [
    {
      "id": 39,
      "name": "production",
      "project_id": 22,
      "created_at": "2025-11-12T06:28:20.000000Z",
      "updated_at": "2025-11-12T06:28:20.000000Z",
      "description": null,
      "uuid": "q0oc84soc8o84cc44ckkkswg"
    }
  ],
  "team_id": 0,
  "created_at": "2025-11-12T06:28:20.000000Z",
  "updated_at": "2025-11-12T06:28:20.000000Z"
}
```

## Error Analysis

### Primary Error Message
```
MCP error -32603: MCP error -32603: Coolify API error: Not found.
```

### Error Response Structure
- **Error Code**: `-32603` (JSON-RPC Internal Error)
- **Error Nesting**: Error is wrapped twice (`MCP error -32603: MCP error -32603`)
- **Coolify API Error**: `Not found`
- **Missing Information**:
  - No HTTP status code provided
  - No full API response body
  - No request URL/method details
  - No stack trace or detailed error context

### Possible Causes

1. **API Endpoint Issue**: The `create_application` endpoint might not exist or might have a different path in Coolify version `4.0.0-beta.426`

2. **Missing Required Parameters**: The API might require additional parameters that aren't documented in the MCP tool definition:
   - Application name (not provided, might be auto-generated)
   - Build configuration
   - Static site detection flag
   - Base directory

3. **Authentication/Permissions**: The API token might not have permissions to create applications, or there might be an authentication issue

4. **Resource Validation**: The API might be validating resources before creation and failing silently:
   - Git repository accessibility
   - Server availability
   - Environment existence

5. **API Version Mismatch**: The MCP tool might be calling an API endpoint that doesn't match the Coolify version being used

## Verified Information

### Coolify Version
- Version: `4.0.0-beta.426`

### Available Resources
- **Server**: `localhost` (UUID: `ewww4cko8gw8sc40ks0k400w`)
  - Status: Reachable and usable
  - IP: `host.docker.internal`
  - Port: 22

- **Project**: `Test_Project` (UUID: `ks4cgko444kw8sso08w8k8c8`)
  - ID: 22
  - Team ID: 0

- **Environment**: `production` (UUID: `q0oc84soc8o84cc44ckkkswg`)
  - ID: 39
  - Project ID: 22

### Git Repository
- URL: `https://github.com/systemnova/help-docs-platform.git`
- Status: Public repository, accessible
- Contains: `index.html`, `Dockerfile`, `README.md`

## MCP Tool Definition

The `create_application` tool expects:
- **Required**: `project_uuid`, `environment_name`, `destination_uuid`
- **Optional**: `environment_uuid`, `git_repository`, `ports_exposes`, `name`, `description`

## Workaround

Since the API call is failing, the application can be created manually through the Coolify web UI:

1. Navigate to Coolify dashboard
2. Go to `Test_Project` → `production` environment
3. Click "Add New Resource" → "Public Repository"
4. Enter repository URL: `https://github.com/systemnova/help-docs-platform.git`
5. Configure:
   - Build Pack: Static
   - Port: 80
   - Is it a static site?: Yes
   - Base Directory: `/`
6. Click "Deploy"

## Recommendations

1. **Check Coolify API Documentation**: Verify the correct endpoint and parameters for creating applications in version 4.0.0-beta.426

2. **Add Debugging**: The MCP tool should provide more detailed error messages, including:
   - HTTP status codes
   - Full API response body
   - Request URL and method

3. **Test API Directly**: Try making the API call directly (via curl or Postman) to see if the issue is with the MCP tool or the Coolify API itself

4. **Check Logs**: Review Coolify server logs to see if there are any errors when the API call is made

5. **Verify Permissions**: Ensure the API token has the necessary permissions to create applications

## Files Created

All necessary files for deployment have been created and pushed to GitHub:
- `index.html` - Hello World HTML page
- `Dockerfile` - Nginx-based static site container
- `README.md` - Project documentation
- Git repository initialized and pushed to GitHub

The application is ready to be deployed once the API issue is resolved or when created manually through the UI.

## Concise Issue Summary

### Critical Issues

1. **API Endpoint Not Found**
   - `create_application` API call returns "Not found" error
   - Error code: `-32603` (JSON-RPC Internal Error)
   - All attempts with valid parameters failed
   - Affects: Programmatic application creation

2. **Insufficient Error Information**
   - No HTTP status codes in error responses
   - No full API response body provided
   - No request URL/method details
   - Error message lacks actionable details

3. **Parameter Validation Inconsistency**
   - Tool requires `environment_name` but accepts `environment_uuid` as optional
   - Using only `environment_uuid` fails validation
   - Using both parameters still results in API error

### Verified Working Functionality

- ✅ Server listing (`list_servers`)
- ✅ Project listing (`list_projects`)
- ✅ Environment listing (`list_environments`)
- ✅ Project details retrieval (`get_project`)
- ✅ Git repository creation and push
- ✅ File creation (HTML, Dockerfile)

### Impact

- **Blocking**: Cannot create applications programmatically via API
- **Workaround Available**: Manual creation through Coolify web UI works
- **Root Cause**: Unknown - requires Coolify API investigation

### Next Steps

1. Verify Coolify API endpoint exists for application creation in v4.0.0-beta.426
2. Check Coolify server logs for detailed error messages
3. Test API endpoint directly (curl/Postman) to isolate MCP tool vs API issue
4. Review Coolify API documentation for correct endpoint and parameters
5. Consider adding more detailed error logging to MCP tool

