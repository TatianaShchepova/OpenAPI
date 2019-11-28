# OpenAPI
OpenAPI specification for CPaaS REST API

## Basic Structure
### You can write OpenAPI definitions in YAML or JSON. In this guide, we use only YAML examples but JSON works equally well. A sample OpenAPI 3.0 definition written in YAML looks like:

```yaml
openapi: 3.0.0
info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9
servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML.
      responses:
        '200':    # status code
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string```
### All keyword names are case-sensitive.

## Metadata
### Every API definition must include the version of the OpenAPI Specification that this definition is based on:

```yaml openapi: 3.0.0```
The OpenAPI version defines the overall structure of an API definition – what you can document and how you document it. OpenAPI 3.0 uses semantic versioning with a three-part version number. The available versions are 3.0.0, 3.0.1, and 3.0.2; they are functionally the same.

### The info section contains API information: title, description (optional), version:

```yaml info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9```
### title is your API name. description is extended information about your API. It can be multiline and supports the CommonMark dialect of Markdown for rich text representation. HTML is supported to the extent provided by CommonMark (see HTML Blocks in CommonMark 0.27 Specification). version is an arbitrary string that specifies the version of your API (do not confuse it with file revision or the openapi version). You can use semantic versioning like major.minor.patch, or an arbitrary string like 1.0-beta or 2017-07-25. info also supports other keywords for contact information, license, terms of service, and other details.

Reference: Info Object.

## Servers
### The servers section specifies the API server and base URL. You can define one or several servers, such as production and sandbox.

```yaml servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing```
All API paths are relative to the server URL. In the example above, /users means http://api.example.com/v1/users or http://staging-api.example.com/users, depending on the server used. For more information, see API Server and Base Path.
## Paths
### The paths section defines individual endpoints (paths) in your API, and the HTTP methods (operations) supported by these endpoints. For example, GET /users can be described as:

```yaml paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML
      responses:
        '200':
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string```
An operation definition includes parameters, request body (if any), possible response status codes (such as 200 OK or 404 Not Found) and response contents. For more information, see Paths and Operations.
## Parameters
### Operations can have parameters passed via URL path (/users/{userId}), query string (/users?role=admin), headers (X-CustomHeader: Value) or cookies (Cookie: debug=0). You can define the parameter data types, format, whether they are required or optional, and other details:

```yaml paths:
  /user/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - name: userId
          in: path
          required: true
          description: Parameter description in CommonMark or HTML.
          schema:
            type : integer
            format: int64
            minimum: 1
      responses: 
        '200':
          description: OK```
For more information, see Describing Parameters.
## Request Body
### If an operation sends a request body, use the requestBody keyword to describe the body content and media type.

```yaml paths:
  /users:
    post:
      summary: Creates a user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                username:
                  type: string
      responses: 
        '201':
          description: Created```
For more information, see Describing Request Body.

## Responses
### For each operation, you can define possible status codes, such as 200 OK or 404 Not Found, and the response body schema. Schemas can be defined inline or referenced via $ref. You can also provide example responses for different content types:

```yaml paths:
  /user/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - name: userId
          in: path
          required: true
          description: The ID of the user to return.
          schema:
            type: integer
            format: int64
            minimum: 1
      responses:
        '200':
          description: A user object.
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                    format: int64
                    example: 4
                  name:
                    type: string
                    example: Jessica Smith
        '400':
          description: The specified user ID is invalid (not a number).
        '404':
          description: A user with the specified ID was not found.
        default:
          description: Unexpected error```
Note that the response HTTP status codes must be enclosed in quotes: "200" (OpenAPI 2.0 did not require this). For more information, see Describing Responses.

## Input and Output Models
### The global components/schemas section lets you define common data structures used in your API. They can be referenced via $ref whenever a schema is required – in parameters, request bodies, and response bodies. For example, this JSON object:

```yaml {
  "id": 4,
  "name": "Arthur Dent"
}```
can be represented as:

```yaml components:
  schemas:
    User:
      properties:
        id:
          type: integer
        name:
          type: string ```
          Both properties are required
     ```yaml required:  
        - id
        - name```
and then referenced in the request body schema and response body schema as follows:

```yaml paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - in: path
          name: userId
          required: true
          type: integer
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
  /users:
    post:
      summary: Creates a new user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'
      responses:
        '201':
          description: Created```
## Authentication
### The securitySchemes and security keywords are used to describe the authentication methods used in your API.

```yaml components:
  securitySchemes:
    BasicAuth:
      type: http
      scheme: basic
security:
  - BasicAuth: []```
### Supported authentication methods are

HTTP authentication: Basic, Bearer, and so on.
API key as a header or query parameter or in cookies
OAuth 2
OpenID Connect Discovery
For more information, see Authentication.

## Full Specification
The full OpenAPI 3.0 Specification is available on GitHub: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md

## Clipped from:
https://swagger.io/docs/specification/basic-structure/
