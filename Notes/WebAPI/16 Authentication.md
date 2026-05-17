# 16. Authentication

Authentication is the process of identifying the user who is making the request to the Web API. Typically, this is done through some form of credentials, such as a username/password, token, or certificates. Once the user is authenticated, the system knows who they are.

## Types of Authentication in Web API

- Basic Authentication: A simple method where the username and password are sent with each request in the HTTP header (not recommended over HTTP without HTTPS).
- Token-based Authentication:
  - JWT (JSON Web Token): A popular method where the server issues a token (JWT) upon successful authentication. The client sends this token with every request, which the server verifies.
  - OAuth: A more advanced authentication protocol often used for granting third-party apps access without sharing the user’s credentials.
- Bearer Authentication: The client sends a bearer token (usually a JWT) in the HTTP header, and the server verifies it.

## Process of Authentication

1. User submits credentials (e.g., username and password, or a token).
2. Server validates credentials using an authentication provider (e.g., database, identity provider).
3. If valid, the server issues a token (JWT, OAuth token, etc.) or identifies the user in the session.
4. Subsequent requests: The client sends the token with each request for verification (commonly in an HTTP header).
