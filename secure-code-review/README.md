# Secure Code Review (SCR)

A practical interview guide for secure code review with methodology, questions, and rules of thumb.

## Golden Rules

- Assume all external input is untrusted until validated.
- Track data flow from source to sink; focus on security boundaries.
- Look for missing authorization, not just broken authentication.
- Prefer safe APIs and libraries; avoid custom crypto or parsers.
- Verify fixes with tests or proofs of mitigation.

## High-Level Methodology

1. Scope and inventory
   - Identify languages, frameworks, build systems, and critical entry points.
2. Build a threat map
   - List sources (inputs), transforms (sanitizers/validators), and sinks (dangerous APIs).
3. Prioritize by risk
   - Internet-facing, auth boundaries, and data sensitivity first.
4. Review by data flow and control flow
   - Follow untrusted data and verify control checks cannot be bypassed.
5. Confirm and document
   - Reproduce or reason about exploitability and propose remediation.

## Foundational Interview Questions

1. What is the difference between static analysis and manual code review? — SAST is automated and broad; manual review is contextual and deeper on logic flaws.
2. How do you prioritize findings? — Focus on exploitability, impact, and exposure (auth boundaries, internet-facing paths).
3. What is a source vs a sink? — Source is untrusted input; sink is a dangerous API or operation.
4. What is a sanitizer or validator? — A function that cleans or enforces constraints on input.
5. Why is output encoding important? — It prevents injection in the output context.
6. What is taint analysis? — Tracking untrusted data through the code to sinks.
7. How do you find missing authorization? — Identify resource access and check server-side authZ for every path.
8. What is a security boundary? — A place where trust changes (user to server, service to service).
9. What is the difference between validation and encoding? — Validation checks input shape; encoding makes output safe.
10. Why are logic bugs hard to find with SAST? — They require business context and workflow understanding.
11. What is an insecure default? — A configuration that is unsafe unless changed.
12. Why are debug logs risky? — They may leak secrets or internal structure.
13. How do you spot auth bypasses? — Follow control flow around checks, caching, and fallbacks.
14. What is the importance of least privilege? — Reduces blast radius if a bug is exploited.
15. What is a safe dependency policy? — Pin versions, review updates, and scan for CVEs.
16. What is a dangerous sink in SSRF? — Any server-side HTTP client or URL fetcher.
17. What is a trust boundary in a microservice system? — The point where identity/permissions must be re-validated.
18. What is an allowlist vs blocklist? — Allowlist permits known-safe values; blocklist denies known-bad.
19. Why is canonicalization important? — It prevents bypasses via encoded or alternative representations.
20. What is a race condition? — A timing issue that breaks invariants (e.g., double-spend).
21. What is insecure deserialization? — Converting untrusted bytes into objects that can execute code.
22. What is mass assignment? — Client sets fields that should be server-controlled.
23. What is context-aware encoding? — Encoding based on HTML/JS/URL/SQL context.
24. What is a privileged action? — An action that requires higher trust (admin, financial, PII).
25. Why are feature flags risky? — They can expose unfinished or insecure paths.

## Methodology Questions

1. Walk me through your process when you are handed 50,000 lines of unfamiliar code. — Start with entry points, auth, data stores, and critical workflows, then trace data flows and controls.
2. How do you decide where to spend time first? — Internet-facing endpoints and high-impact data.
3. How do you review code in a new framework? — Learn the framework data flow and security defaults, then review against them.
4. How do you document a finding? — Impact, affected path, exploit scenario, and remediation.
5. How do you validate a fix? — Code review plus tests or a local proof.
6. How do you map entry points quickly? — Start with routing/controllers and public APIs.
7. How do you identify data stores and trust boundaries? — Track DB clients, queues, caches, and third-party calls.
8. How do you review authZ at scale? — Build an authZ map and check every resource access.
9. How do you handle unknown custom crypto? — Flag for replacement with vetted libraries.
10. How do you spot logic flaws? — Verify workflows and state transitions against business rules.
11. How do you review error handling? — Ensure errors don’t leak secrets or bypass checks.
12. How do you review third-party integrations? — Validate input/output contracts and least privilege.
13. How do you review caching layers? — Check for user data leakage or permission mismatches.
14. How do you validate input sanitizers? — Confirm they match the sink context and are comprehensive.
15. How do you communicate risk to stakeholders? — Provide severity, impact, and clear remediation steps.

## Tooling Questions (SAST + Review)

1. What are the pros and cons of SAST tools? — Pros: coverage and speed. Cons: false positives, limited business context.
2. How do you handle false positives? — Triage, add suppression with justification, and tune rules.
3. How do you compare tools like Checkmarx, Snyk, and Semgrep? — Scope, rules quality, integration, and noise levels.
4. How do you use Semgrep effectively? — Start with standard rules, then add custom rules based on patterns.
5. When is SAST not enough? — For logic flaws, authZ gaps, and insecure design issues.
6. How do you tune SAST rules for a large codebase? — Start with high-signal rules and expand gradually.
7. How do you prevent alert fatigue? — Prioritize critical findings and suppress validated false positives.
8. How do you validate SAST findings? — Reproduce the data flow from source to sink.
9. How do you combine SAST with manual review? — Use SAST to triage and manual review for logic paths.
10. How do you manage rule drift over time? — Review rules quarterly and align with new threats.
11. How do you use CodeQL in review? — Write queries for project-specific sinks and flows.
12. How do you handle missing coverage in SAST? — Add custom rules or manual checks.
13. How do you evaluate a new tool? — Pilot on representative services and measure signal/noise.
14. How do you integrate findings into SDLC? — Create tickets with owners and SLAs.
15. How do you handle secrets scanners in SCR? — Treat as high priority and rotate immediately.

## Advanced Topics

1. How do you review Terraform for security? — Check IAM, public exposure, encryption, and logging defaults.
2. How do you review CloudFormation for misconfigurations? — Look for wildcard IAM, public buckets, and open security groups.
3. How do you review K8s YAML? — Verify pod security, RBAC, network policies, and secrets handling.
4. What is the biggest IaC review pitfall? — Assuming defaults are secure.
5. How do you validate IaC fixes? — Policy-as-code checks and drift monitoring.
6. How do you review IAM policies quickly? — Look for wildcard actions/resources and missing conditions.
7. How do you assess public exposure in IaC? — Check open ingress, public buckets, and public IPs.
8. How do you review serverless configs? — Verify least privilege, timeouts, and environment secrets.
9. How do you validate encryption requirements? — Ensure KMS/CMKs are enforced and TLS is required.
10. How do you review container runtime configs? — Check for privileged mode, hostPath, and capabilities.
11. How do you assess logging and monitoring in IaC? — Ensure audit logs and alerts are enabled.
12. How do you handle drift between IaC and runtime? — Enforce GitOps and periodic reconciles.
13. How do you review network segmentation? — Verify private subnets and restrictive security groups.
14. How do you handle IaC module risks? — Pin module versions and review sources.
15. How do you test IaC for security? — Use policy-as-code and pre-merge checks.

## Resources

- OWASP Code Review Guide: https://owasp.org/www-project-code-review-guide/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/
- Semgrep Rules: https://semgrep.dev/explore
- CodeQL Queries: https://github.com/github/codeql
- Secure Code Review Checklist: https://github.com/OWASP/Code-Review-Guide
