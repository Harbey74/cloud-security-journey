# VPC Command Reference

## Describe VPC

```bash
aws ec2 describe-vpcs \
  --vpc-ids vpc-06b79957438259a36 \
  --profile skyshield \
  --region eu-west-1 \
  --query "Vpcs[].{VPC:VpcId,CIDR:CidrBlock,State:State,Tenancy:InstanceTenancy}"
```

Purpose:
Displays VPC configuration.

---

## Describe Subnets

```bash
aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=vpc-06b79957438259a36" \
  --profile skyshield \
  --region eu-west-1 \
  --query "Subnets[].{SubnetId:SubnetId,CIDR:CidrBlock,AZ:AvailabilityZone,AvailableIPs:AvailableIpAddressCount,MapPublicIP:MapPublicIpOnLaunch}"
```

Purpose:
Lists subnets within the VPC.

---

## Describe Route Tables

```bash
aws ec2 describe-route-tables \
  --filters "Name=vpc-id,Values=vpc-06b79957438259a36" \
  --profile skyshield \
  --region eu-west-1 \
  --query "RouteTables[].{RouteTableId:RouteTableId,Main:Associations[?Main==`true`].Main,Associations:Associations[].SubnetId,Routes:Routes[].{Destination:DestinationCidrBlock,Target:GatewayId,NAT:NatGatewayId,State:State}}"
```

Purpose:
Displays routing configuration.

---

## Describe Internet Gateway

```bash
aws ec2 describe-internet-gateways \
  --filters "Name=attachment.vpc-id,Values=vpc-06b79957438259a36" \
  --profile skyshield \
  --region eu-west-1 \
  --query "InternetGateways[].{IGW:InternetGatewayId,State:Attachments[0].State,VPC:Attachments[0].VpcId}"
```

Purpose:
Verifies the attached Internet Gateway.

---

## Describe NAT Gateways

```bash
aws ec2 describe-nat-gateways \
  --filter "Name=vpc-id,Values=vpc-06b79957438259a36" \
  --profile skyshield \
  --region eu-west-1 \
  --query "NatGateways[].{NAT:NatGatewayId,State:State,Subnet:SubnetId,PublicIP:NatGatewayAddresses[0].PublicIp,PrivateIP:NatGatewayAddresses[0].PrivateIp}"
```

Purpose:
Checks whether NAT Gateways exist in the VPC.

---

## Describe Network ACL

```bash
aws ec2 describe-network-acls \
  --filters "Name=association.subnet-id,Values=subnet-0c78da000a4442dd9" \
  --profile skyshield \
  --region eu-west-1
```

Purpose:
Displays subnet-level filtering rules.

---

## Check DNS Support

```bash
aws ec2 describe-vpc-attribute \
  --vpc-id vpc-06b79957438259a36 \
  --attribute enableDnsSupport \
  --profile skyshield \
  --region eu-west-1
```

Purpose:
Verifies Amazon-provided DNS is enabled.

---

## Check DNS Hostnames

```bash
aws ec2 describe-vpc-attribute \
  --vpc-id vpc-06b79957438259a36 \
  --attribute enableDnsHostnames \
  --profile skyshield \
  --region eu-west-1
```

Purpose:
Verifies DNS hostnames are enabled.