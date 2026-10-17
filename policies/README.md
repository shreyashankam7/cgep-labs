# Compliance Policy Library

Rego policies that evaluate a Terraform plan (`terraform show -json`) and
produce a `deny` set. An empty set means compliant.

| Policy file | Control | Framework | Severity | What it requires | Remediation |
|---|---|---|---|---|---|
| `sc28_encryption.rego` | SC-28 | NIST 800-53 | high | Every `google_storage_bucket` uses a customer-managed encryption key (CMEK). | Add an `encryption { default_kms_key_name = ... }` block. |
| `ac3_no_public.rego` | AC-3 | NIST 800-53 | critical | Buckets enforce uniform access and public access prevention; firewalls do not open ports 22 or 3389 to `0.0.0.0/0`. | Set `uniform_bucket_level_access = true` and `public_access_prevention = "enforced"`; narrow firewall `source_ranges`. |
| `cm6_required_tags.rego` | CM-6 | NIST 800-53 | medium | Every labelable resource carries `project`, `environment`, `managed_by`, `compliance_scope`. | Add the missing labels. |

## Running

    opa test -v policies/
    opa eval -d policies -i <plan.json> data.compliance.sc28.deny --format=pretty

## Tests

Each policy has a passing and a failing case in `policies/tests/`.
