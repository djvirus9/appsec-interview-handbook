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

## Methodology Questions

1. Walk me through your process when you are handed 50,000 lines of unfamiliar code. — Start with entry points, auth, data stores, and critical workflows, then trace data flows and controls.
2. How do you decide where to spend time first? — Internet-facing endpoints and high-impact data.
3. How do you review code in a new framework? — Learn the framework data flow and security defaults, then review against them.
4. How do you document a finding? — Impact, affected path, exploit scenario, and remediation.
5. How do you validate a fix? — Code review plus tests or a local proof.

## Tooling Questions (SAST + Review)

1. What are the pros and cons of SAST tools? — Pros: coverage and speed. Cons: false positives, limited business context.
2. How do you handle false positives? — Triage, add suppression with justification, and tune rules.
3. How do you compare tools like Checkmarx, Snyk, and Semgrep? — Scope, rules quality, integration, and noise levels.
4. How do you use Semgrep effectively? — Start with standard rules, then add custom rules based on patterns.
5. When is SAST not enough? — For logic flaws, authZ gaps, and insecure design issues.

## Advanced Topics

1. How do you review Terraform for security? — Check IAM, public exposure, encryption, and logging defaults.
2. How do you review CloudFormation for misconfigurations? — Look for wildcard IAM, public buckets, and open security groups.
3. How do you review K8s YAML? — Verify pod security, RBAC, network policies, and secrets handling.
4. What is the biggest IaC review pitfall? — Assuming defaults are secure.
5. How do you validate IaC fixes? — Policy-as-code checks and drift monitoring.

## Resources

- OWASP Code Review Guide: https://owasp.org/www-project-code-review-guide/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/
- Semgrep Rules: https://semgrep.dev/explore
- CodeQL Queries: https://github.com/github/codeql
- Secure Code Review Checklist: https://github.com/OWASP/Code-Review-Guide
