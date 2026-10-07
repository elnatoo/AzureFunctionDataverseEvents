# Security Policy

<!--## Supported Versions

I plan to monitor and patch vulnerabilities only in the current major release branch. 

| Version | Supported          |
| ------- | ------------------ |
| 1.x     | :white_check_mark: |
| < 1.0   | :x:                |

-->

## Reporting a Vulnerability

⚠️ **Please do not open GitHub issues for security vulnerabilities.**

To report a security vulnerability, please use the **GitHub Security Advisory** feature on this repository:
1. Navigate to the **Security and quality** tab of the repository.
2. Click on **Advisories** on the left sidebar.
3. Click **Report a vulnerability** to open a private draft advisory.

### What to Include in a Report
* A description of the vulnerability.
* Steps to reproduce the exploit (or a proof-of-concept script).
* The potential impact (e.g. Remote Code Execution, Privilege Escalation, Data Leakage).
* (Optional, but welcomed) Any suggested remediation steps.

We will acknowledge receipt of your report within **48 hours** and provide a timeline for triage and patching.

## Repository-Specific Security Guidelines

Because this project processes and logs active corporate data structures from Microsoft Dataverse, users deploying this repository should adhere to the following security guardrails:

### Guard Against Log Injection & PII Exposure
* **The Risk:** This application logs raw Dataverse headers, query parameters, and target attributes for debugging purposes. 
* **The Policy:** Do not deploy this function to production with verbose logging enabled if your Dataverse environment processes Personally Identifiable Information (PII), protected health data, or financial financial records. 
* **Remediation:** Implement log filtering or tokenization in `DataverseRequestInspector.cs` before outputs are transmitted to Azure Monitor or Application Insights.

### Secret and Credential Management
* **The Risk:** Leaking API keys, Azure Function master keys, or Dataverse webhook validation tokens.
* **The Policy:** The `local.settings.json` file is explicitly included in the `.gitignore`. Never commit actual connections strings, client secrets, or function authorization keys to source control.
* **Remediation:** Use Azure Key Vault references within your production Azure Function environment configuration to resolve secrets dynamically at runtime.

### Endpoint Authentication
* **The Risk:** Unauthorized actors sending spoofed HTTP POST payloads to the function endpoint, leading to denial of service (DoS attack) or poisoned logs.
* **The Policy:** The `[HttpTrigger]` must be explicitly configured with an authorization level of `AuthorizationLevel.Function` or `AuthorizationLevel.Admin`. 
* **Remediation:** Validate the `x-ms-dynamics-organization` header or implement custom signature verification within the request pipeline to ensure incoming traffic originates strictly from your verified Microsoft Dataverse instance.
