This module provisions a compliant Google Cloud Storage bucket protected by a customer-managed encryption key (CMEK). It enforces SC-12 (KMS key

establishment, owned by the project rather than Google-managed), SC-13 and

SC-28 (cryptographic protection at rest via a rotating 90-day KMS key),

AU-11 (data retention policy, enforced per-environment), and CM-6 (uniform

bucket-level access, enforced public access prevention, and required

compliance labels merged automatically). Consumers can only customize

project, environment, retention period, and naming — every

compliance-relevant setting is hardcoded inside the module.

