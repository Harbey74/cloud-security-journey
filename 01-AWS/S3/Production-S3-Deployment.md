# Production S3 Deployment

## Objective

## Date
03 August 2026

Secure an S3 bucket using AWS security best practices.

## Security Controls Implemented

- Block Public Access
- SSE-S3 Encryption
- Versioning
- Resource Tagging

## Validation Commands
aws s3api get-public-access-block --bucket skyshield-abiodun-2026 --profile skyshield
aws s3api get-bucket-encryption --bucket skyshield-abiodun-2026 --profile skyshield
aws s3api get-bucket-versioning --bucket skyshield-abiodun-2026 --profile skyshield
aws s3api put-bucket-versioning --bucket skyshield-abiodun-2026 --versioning-configuration Status=Enabled --profile skyshield
aws s3api get-bucket-tagging --bucket skyshield-abiodun-2026 --profile skyshield

aws s3api put-bucket-tagging --bucket skyshield-abiodun-2026 --tagging "TagSet=[{Key=Project,Value=Skyshield},{Key=Environment,Value=Lab},{Key=Owner,Value=Abiodun},{Key=Purpose,Value=CloudSecurityLearning}]" --profile skyshield

## Results

(All controls successfully validated)

## Lessons Learned

- Always validate after configuration.
- Versioning is disabled by default.
- Encryption should be enabled by default.
- Governance tags simplify resource management.