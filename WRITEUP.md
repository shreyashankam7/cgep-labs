# CGE-P Capstone Write-up

## Lab 4.4: Evidence chain of custody

Every pull request runs the `grc-gate` workflow, which plans the Terraform, runs the Conftest policy gate and the tfsec scan, bundles the results, signs the bundle with Sigstore (keyless, via GitHub OIDC), and stores it in an Object Lock vault. An assessor can verify any run with one command:

    EVIDENCE_VAULT=cgep-lab-grc-evidence-vault-6a3af42c bash scripts/verify-evidence.sh <run_id> --profile default

### The four properties and what proves each

| Property | What it means | Artifact that proves it |
|---|---|---|
| Authenticity | The evidence came from this repo's workflow | The Cosign signature bundle (`*.tar.gz.sig.bundle`): a Fulcio certificate bound to the GitHub OIDC identity, logged in Rekor. Checked by `cosign verify-blob` in `scripts/verify-evidence.sh` |
| Integrity | The evidence has not changed since it was produced | The `.sha256` sidecar and the hash in `evidence/lab-4-4/receipt.json`, recomputed and compared by the verify script. Tamper test: appending one line changed the hash from `a187f1a2...` to `08c3725c...` and Cosign rejected the file |
| Timeliness | There is a trustworthy record of when it was produced | The Rekor transparency log entry referenced inside the signature bundle, plus the S3 object timestamps and the `version_id` in the receipt |
| Preservation | The evidence is still there and protected | S3 Object Lock and versioning on the vault `cgep-lab-grc-evidence-vault-6a3af42c`. The verify script checks that `RetainUntilDate` has not passed |

### Evidence references

- Verified run: `40409380142` (commit `236c480087f1850f047007717e8c24c35a05495b`), result `CHAIN INTACT`
- Receipt: `evidence/lab-4-4/receipt.json`
- Pipeline: `.github/workflows/grc-gate.yml`
- Verifier: `scripts/verify-evidence.sh`

### Design notes

- The signing and upload steps run with `if: always()`, so a failing gate still leaves signed evidence. The build is failed afterwards by the final `Enforce gate` step.
- The workflow's AWS role is read-only plus write access to the evidence vault only (`vault-write` inline policy).
- Object Lock refuses overwrites, so a tampered copy can exist only outside the vault.
- The tfsec rule `aws-s3-encryption-customer-key` is suppressed with a documented risk acceptance for the lab bucket (SSE-S3 satisfies SC-28 here).
