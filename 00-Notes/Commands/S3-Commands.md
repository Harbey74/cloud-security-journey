# Amazon S3 CLI Commands

## List Buckets

```bash
aws s3 ls --profile skyshield
```

## List Objects

```bash
aws s3 ls s3://<bucket-name> --profile skyshield
```

## Upload an Object

```bash
aws s3 cp lab1.txt s3://<bucket-name>/ --profile skyshield
```

## Download an Object

```bash
aws s3 cp s3://<bucket-name>/lab1.txt .
```

## View Object Metadata

```bash
aws s3api head-object --bucket <bucket-name> --key lab1.txt --profile skyshield
```

## Inspect Bucket Public Access Settings
aws s3api get-public-access-block --bucket <your-bucket-name> --profile skyshield

## Get bucket region
aws configure get region --profile skyshield

## Get bucket location
aws s3api get-bucket-location --bucket skyshield-abiodun-2026 --profile skyshield

## Get bucket encryption status
aws s3api get-bucket-encryption --bucket skyshield-abiodun-2026 --profile skyshield

## Get bucket versioning status
aws s3api get-bucket-versioning --bucket skyshield-abiodun-2026 --profile skyshield

## Enable bucket versioning
aws s3api put-bucket-versioning --bucket skyshield-abiodun-2026 --versioning-configuration Status=Enabled --profile skyshield

## Get bucket tags
aws s3api get-bucket-tagging --bucket skyshield-abiodun-2026 --profile skyshield

## Set bucket tags
aws s3api put-bucket-tagging --bucket skyshield-abiodun-2026 --tagging "TagSet=[{Key=Project,Value=Skyshield},{Key=Environment,Value=Lab},{Key=Owner,Value=Abiodun},{Key=Purpose,Value=CloudSecurityLearning}]" --profile skyshield


