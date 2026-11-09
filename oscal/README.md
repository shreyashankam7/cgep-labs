# OSCAL artifacts (Lab 6.1)

## Component definition: `components/compliant-s3.json`
Describes the `compliant-s3` Terraform module (`terraform/primitives/compliant-s3`).
It claims four NIST 800-53 Rev 5 controls, each mapped to the Terraform resource that enforces it:

| Control | Terraform resource |
|---|---|
| sc-28 | aws_s3_bucket_server_side_encryption_configuration.primary |
| ac-3 | aws_s3_bucket_public_access_block.primary |
| au-3 | aws_s3_bucket_logging.primary |
| cm-6 | aws_s3_bucket.primary |

## Profile: `profiles/cge-p-minimum.json`
Selects controls sc-28, ac-3, au-3 and cm-6 from the NIST 800-53 Rev 5 catalog.

## Evidence
Each implemented requirement links (rel=evidence) to the signed pipeline bundle for run 41544880018:
`s3://cgep-lab-grc-evidence-vault-6a3af42c/runs/41544880018/`

Verification result: `verify-evidence.sh` confirmed the checksum and signature ("Verified OK").
The final retention check failed because the Object Lock retention on this bundle
expired on 2026-11-03. The evidence is authentic and unaltered; only its lock window
lapsed. A fresh pipeline run will be linked for the capstone.

## Validation
Both files pass `trestle validate` (see `evidence/lab-6-1/trestle-validate.txt`).
