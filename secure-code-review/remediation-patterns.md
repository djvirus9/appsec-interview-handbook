# Remediation Patterns

Common fixes to recommend during secure code reviews.

## Input Validation

- Validate type, length, and format at the boundary.
- Use allowlists for fields, methods, and file types.
- Reject unexpected parameters and duplicated keys.

## Output Encoding

- Encode by context (HTML, JS, URL, SQL).
- Use framework-provided encoders.
- Do not trust client-side encoding alone.

## Parameterized Queries

- Use prepared statements for SQL and NoSQL.
- Avoid string concatenation for queries.
- Apply least privilege for DB users.

## Safe Deserialization

- Prefer JSON or protobuf over native object serialization.
- Use allowlisted types if deserialization is necessary.
- Enforce size limits and schema validation.

## SSRF Prevention

- Allowlist destination hosts and schemes.
- Block internal IP ranges and metadata endpoints.
- Resolve and pin IPs to prevent DNS rebinding.

## Secure File Handling

- Normalize and validate paths.
- Store uploads outside the web root.
- Scan and sanitize uploaded content.

## Auth and Session Hardening

- Enforce strong auth and MFA for sensitive actions.
- Use short-lived tokens with rotation and revocation.
- Log all access control decisions.

## Error Handling and Logging

- Use safe error messages for users.
- Redact secrets and PII in logs.
- Centralize logging and alert on anomalies.

## Dependency Management

- Pin versions and use lockfiles.
- Scan dependencies and update regularly.
- Generate SBOMs for releases.

## IaC Hardening

- Enforce policies via policy-as-code.
- Disable public access by default.
- Enable encryption and logging in all services.
