# EC2 Command Reference

## Describe EC2 Instance

```bash
aws ec2 describe-instances \
  --profile skyshield \
  --region eu-west-1 \
  --query "Reservations[].Instances[].{InstanceId:InstanceId,State:State.Name,InstanceType:InstanceType,AMI:ImageId,AZ:Placement.AvailabilityZone,VPC:VpcId,Subnet:SubnetId,PrivateIP:PrivateIpAddress,PublicIP:PublicIpAddress,SecurityGroups:SecurityGroups[*].GroupId,IAMProfile:IamInstanceProfile.Arn}"
```

Purpose:
Displays key information about an EC2 instance.

---

## Describe Elastic Network Interface (ENI)

```bash
aws ec2 describe-network-interfaces \
  --filters "Name=attachment.instance-id,Values=i-080fce8271d296ccf" \
  --profile skyshield \
  --region eu-west-1 \
  --query "NetworkInterfaces[].{ENI:NetworkInterfaceId,PrivateIP:PrivateIpAddress,MAC:MacAddress,Subnet:SubnetId,VPC:VpcId,SecurityGroups:Groups[*].GroupId,Status:Status,PublicIP:Association.PublicIp}"
```

Purpose:
Displays networking information attached to the EC2 instance.

---

## Describe Root EBS Volume

```bash
aws ec2 describe-volumes \
  --filters "Name=attachment.instance-id,Values=i-080fce8271d296ccf" \
  --profile skyshield \
  --region eu-west-1 \
  --query "Volumes[].{VolumeId:VolumeId,Size:Size,Type:VolumeType,State:State,Encrypted:Encrypted,Device:Attachments[0].Device}"
```

Purpose:
Displays storage attached to the EC2 instance.

---

## Describe Security Group

```bash
aws ec2 describe-security-groups \
  --group-ids sg-0ffc64da5c4cd8b49 \
  --profile skyshield \
  --region eu-west-1
```

Purpose:
Displays inbound and outbound Security Group rules.

---

## Verify IAM Role

Run from inside EC2.

```bash
aws sts get-caller-identity
```

Purpose:
Confirms the EC2 instance is using its IAM Role.

---

## Check Resolver Configuration

Run from inside EC2.

```bash
cat /etc/resolv.conf
```

Purpose:
Displays the DNS resolver being used.

---

## Resolve a Hostname

Run from inside EC2.

```bash
getent hosts example.com
```

Purpose:
Verifies DNS resolution.

---

## Test IPv4 Connectivity

Run from inside EC2.

```bash
curl -4 -I https://example.com
```

Purpose:
Verifies outbound IPv4 connectivity.

---

## Test IPv6 Connectivity

Run from inside EC2.

```bash
curl -6 -I https://example.com
```

Purpose:
Verifies outbound IPv6 connectivity.