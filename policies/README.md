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

## Cloud variants

A control ID is portable across clouds, but a Rego rule that names a resource type is not. Each control therefore has one file per cloud, with the same control ID.

| Control | GCP file (Lab 3.3) | AWS file (Lab 3.4) | AWS Conftest namespace |
|---|---|---|---|
| SC-28 | `sc28_encryption.rego` | `sc28_encryption_aws.rego` | `compliance.sc28_aws` |
| AC-3 | `ac3_no_public.rego` | `ac3_no_public_aws.rego` | `compliance.ac3_aws` |
| CM-6 | `cm6_required_tags.rego` | `cm6_required_tags_aws.rego` | `compliance.cm6_aws` |

The AWS variants match encryption and public-access-block resources to buckets by reference (`configuration`), because bucket names are unknown at plan time.

## Policy gate

`scripts/policy-gate.sh --workspace <dir>` runs the three AWS namespaces against a saved plan and exits 0 (pass) or 1 (fail). Only AWS namespaces are included, because a GCP namespace passes an AWS plan with zero coverage.
