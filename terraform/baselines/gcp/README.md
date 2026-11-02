# GCP Security Services Baseline (Lab 5.4)

Project: `cgep-lab-2026` (standalone project, no Organization).

| Control area | Resources | NIST 800-53 |
|---|---|---|
| Workload Identity Federation | Pool `github-actions`, OIDC provider trusting GitHub, condition bound to `shreyashankam7/cgep-labs`, read-only service account `cgep-grc-gate-sa` (roles/viewer) | AC-2, IA-2, IA-5 (no downloadable service-account keys) |
| Data Access audit logs | DATA_READ, DATA_WRITE, ADMIN_READ for storage, cloudkms and iam | AU-2, AU-12 |
| Org Policy (preventive) | Not applied, see below | CM-6, AC-2, AC-3 |

## Lesson: Data Access logs are off by default

GCP records admin activity automatically, but reads and writes of data are not logged until enabled per service. Almost nobody turns them on, which makes this one of the most common GCP audit findings. This baseline enables them in `audit_logs.tf`. Evidence: `evidence/lab-5-4/iam-policy.json`.

## Finding: Org Policy could not be enforced on this project

`org_policy.tf.disabled` holds three constraints that reject bad actions at the API (`storage.uniformBucketLevelAccess`, `iam.disableServiceAccountKeyCreation`, `compute.requireOsLogin`). Applying them failed with `Permission 'orgpolicy.policies.create' denied`. The role that carries that permission, `roles/orgpolicy.policyAdmin`, can only be granted at Organization level, and it cannot be recreated as a custom role (`orgpolicy.policies.create is not supported in custom roles`). A personal project without an Organization therefore cannot use Org Policy at all.

Impact: the preventive layer is missing. Without it, nothing stops a service-account key from being created or a bucket from using fine-grained ACLs. Remediation: place the project in an Organization (Cloud Identity or Workspace), grant `roles/orgpolicy.policyAdmin` at the organization, and rename `org_policy.tf.disabled` back to `org_policy.tf`.

## Notes

- The Google provider needs `user_project_override = true` and `billing_project` to use the project as quota project. Without them the orgpolicy API call is billed to Google's shared gcloud project and fails with SERVICE_DISABLED.
- The WIF pool enters a 30 day soft delete when destroyed, so the same pool ID cannot be recreated until it expires.
