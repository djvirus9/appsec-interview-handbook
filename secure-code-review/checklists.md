# Secure Code Review Checklists

Language-specific items to look for during review.

## General Checklist

- Authentication and authorization checks on all sensitive actions
- Input validation and output encoding by context
- Use of safe APIs (parameterized queries, safe serializers)
- Secrets management and no hardcoded credentials
- Proper logging without sensitive data leakage
- Dependency and supply-chain risk controls
- Secure error handling and no debug info in prod

## Java

- Use of `PreparedStatement` for DB access
- Avoid Java native serialization on untrusted data
- Safe XML parsing (`XXE` protections)
- Proper validation for Spring controllers
- Use of `@PreAuthorize`/`@PostAuthorize` or centralized authZ
- TLS config and hostname verification

## Python

- Avoid `eval`, `exec`, and `pickle` on untrusted input
- Use parameterized SQL in DB libraries
- Safe subprocess usage (`subprocess` without shell)
- Proper URL validation to prevent SSRF
- Secure deserialization (JSON over pickle)
- Input validation via schemas (Pydantic, Marshmallow)

## C/C++

- Bounds checking on buffers and arrays
- Use safe string APIs and avoid `strcpy`/`sprintf`
- Memory lifecycle: avoid use-after-free/double free
- Use of modern C++ containers (`std::string`, `std::vector`)
- Integer overflows in size calculations
- Secure compilation flags (`-fstack-protector`, ASLR)

## JavaScript (Node.js)

- Avoid `eval` and unsafe dynamic requires
- Validate and sanitize request bodies and params
- Use `helmet` and proper security headers
- Prevent prototype pollution (`__proto__`, `constructor`)
- Safe file path handling (`path.join` + allowlists)
- Ensure async errors are handled and not ignored

## Go

- Use context timeouts for external calls
- Validate JSON input with struct tags
- Use `database/sql` with prepared statements
- Check for SSRF on URL fetchers
- Avoid `os/exec` with untrusted inputs

## IaC (Terraform/CloudFormation)

- No public S3 buckets or open security groups
- Enforce encryption at rest and in transit
- Avoid wildcard IAM policies
- Enable audit logging and monitoring
- Use private subnets and minimal ingress
