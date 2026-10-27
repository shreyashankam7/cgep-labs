# AWS Security Services Baseline (Lab 5.2)

Account-level services that produce evidence continuously, independent of the pull-request pipeline.

| Service | Resources | NIST 800-53 controls |
|---|---|---|
| CloudTrail | Multi-region trail `cgep-lab-mgmt`, management events, log-file validation, encrypted log bucket with public access blocked | AU-2, AU-12 (event logging), AU-10 (integrity of audit information via signed digest files) |
| Security Hub | Enabled with NIST 800-53 Rev 5 and AWS Foundational Security Best Practices standards | RA-5 (vulnerability monitoring), SI-4 (system monitoring) |
| AWS Config | Recorder, delivery channel and encrypted log bucket (config.tf). Added because the Security Hub standards stayed INCOMPLETE without a recorder. | CM-2, CM-6, CM-8 |

## Evidence

- `evidence/lab-5-2/security-hub-findings.json`: Security Hub findings captured from the account.

## Notes

- Default Security Hub standards are disabled (`enable_default_standards = false`) so only the two chosen standards are subscribed and billed.
- This baseline bills while running. Destroy with `terraform destroy` after capturing evidence.
- Config was added beyond the lab guide: with no recorder, both Security Hub standards reported NO_AVAILABLE_CONFIGURATION_RECORDER and produced no findings.
- Captured 16 Security Hub finding(s). The account is nearly empty, so few controls had anything to flag.
- The baseline was destroyed after evidence capture to stop billing.
