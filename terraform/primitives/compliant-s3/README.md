This module provisions a compliant S3 bucket for storing data, alongside a

dedicated logging bucket that records all access to it. It enforces

SC-28 (encryption at rest via AES256), AU-3 and AU-6 (access logging,

written to a separate log bucket), CM-6 (versioning enabled and required

compliance tags applied automatically via provider default\_tags), and

AC-3 (all four public access block settings enabled, blocking any public

access to the bucket).

