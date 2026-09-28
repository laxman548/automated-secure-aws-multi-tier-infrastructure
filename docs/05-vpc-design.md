\# Phase 3 - AWS Networking



\## 1. Objective



The objective of this phase is to design and configure the secure AWS network foundation for the multi-tier application.



The network separates public-facing infrastructure from private application resources and future database resources.



The network is deployed across two Availability Zones for improved availability.



\---



\## 2. Architecture Diagram



```text

&#x20;                             INTERNET

&#x20;                                 |

&#x20;                                 v

&#x20;                   +-------------------------+

&#x20;                   |    Internet Gateway     |

&#x20;                   |          IGW            |

&#x20;                   +------------+------------+

&#x20;                                |

&#x20;               +----------------+----------------+

&#x20;               |                                 |

&#x20;               v                                 v

&#x20;      +-------------------+             +-------------------+

&#x20;      |   PUBLIC SUBNET A |             |   PUBLIC SUBNET B |

&#x20;      |   10.50.1.0/24    |             |   10.50.2.0/24    |

&#x20;      |   ap-south-1a      |             |   ap-south-1b      |

&#x20;      +---------+----------+             +-------------------+

&#x20;                |

&#x20;                v

&#x20;      +-------------------+

&#x20;      |    NAT Gateway    |

&#x20;      |   Public Subnet A |

&#x20;      |  Elastic IP: EIP  |

&#x20;      +---------+---------+

&#x20;                |

&#x20;                v

&#x20;      +-------------------+

&#x20;      | PRIVATE ROUTE     |

&#x20;      |      TABLE        |

&#x20;      +---------+---------+

&#x20;                |

&#x20;         +------+------+

&#x20;         |             |

&#x20;         v             v

&#x20;+----------------+  +----------------+

&#x20;| PRIVATE SUBNET |  | PRIVATE SUBNET |

&#x20;|       A        |  |       B        |

&#x20;| 10.50.11.0/24 |  | 10.50.12.0/24  |

&#x20;| ap-south-1a    |  | ap-south-1b    |

&#x20;|                |  |                |

&#x20;| Future App     |  | Future App     |

&#x20;| Server         |  | Server         |

&#x20;+----------------+  +----------------+

```



\### Future Application Architecture



```text

&#x20;                        INTERNET

&#x20;                           |

&#x20;                           v

&#x20;                 +----------------------+

&#x20;                 | Application Load     |

&#x20;                 |      Balancer        |

&#x20;                 |       PUBLIC         |

&#x20;                 +----------+-----------+

&#x20;                            |

&#x20;                +-----------+-----------+

&#x20;                |                       |

&#x20;                v                       v

&#x20;         +--------------+        +--------------+

&#x20;         | App Server 1 |        | App Server 2 |

&#x20;         |   PRIVATE    |        |   PRIVATE    |

&#x20;         +------+-------+        +------+-------+

&#x20;                |                       |

&#x20;                +-----------+-----------+

&#x20;                            |

&#x20;                            v

&#x20;                   +----------------+

&#x20;                   |    DATABASE    |

&#x20;                   |    PRIVATE     |

&#x20;                   +----------------+



&#x20;                   OUTBOUND INTERNET

&#x20;                           ^

&#x20;                           |

&#x20;                   +-------+-------+

&#x20;                   |  NAT Gateway  |

&#x20;                   |    PUBLIC     |

&#x20;                   +---------------+

```



\---



\## 3. AWS Region



\*\*Region Used:\*\* `ap-south-1` (AWS Mumbai Region)



\*\*Verify Region\*\*



```bash

aws configure get region

```



Expected:



```

ap-south-1

```



\---



\## 4. Availability Zones



Two Availability Zones were selected:



\- `ap-south-1a`

\- `ap-south-1b`



\*\*Verify Availability Zones\*\*



```bash

aws ec2 describe-availability-zones `

&#x20; --region ap-south-1 `

&#x20; --query "AvailabilityZones\[].{Name:ZoneName,State:State}" `

&#x20; --output table

```



\---



\## 5. VPC Design



\*\*VPC CIDR:\*\* `10.50.0.0/16`



The VPC provides the overall private IP address space for the project.



\*\*Create VPC\*\*



```bash

aws ec2 create-vpc `

&#x20; --cidr-block 10.50.0.0/16

```



The VPC was created with:



\- VPC ID: `vpc-00d0bbdb7cd548a87`

\- CIDR: `10.50.0.0/16`



\*\*Add VPC Name Tag\*\*



```bash

aws ec2 create-tags `

&#x20; --resources vpc-00d0bbdb7cd548a87 `

&#x20; --tags Key=Name,Value=secure-multi-tier-vpc

```



\*\*Verify VPC\*\*



```bash

aws ec2 describe-vpcs `

&#x20; --vpc-ids vpc-00d0bbdb7cd548a87 `

&#x20; --query "Vpcs\[0].{VPC:VpcId,CIDR:CidrBlock,State:State,Name:Tags\[?Key=='Name']|\[0].Value}" `

&#x20; --output table

```



Final VPC:



| Property | Value |

|---|---|

| Name | secure-multi-tier-vpc |

| VPC ID | vpc-00d0bbdb7cd548a87 |

| CIDR | 10.50.0.0/16 |

| State | available |



\---



\## 6. Subnet Design



The VPC is divided into four subnets:



| Subnet | Type | CIDR | Availability Zone |

|---|---|---|---|

| Public A | Public | 10.50.1.0/24 | ap-south-1a |

| Public B | Public | 10.50.2.0/24 | ap-south-1b |

| Private A | Private | 10.50.11.0/24 | ap-south-1a |

| Private B | Private | 10.50.12.0/24 | ap-south-1b |



The CIDR ranges do not overlap.



\---



\## 7. Create Public Subnet A



```bash

aws ec2 create-subnet `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --cidr-block 10.50.1.0/24 `

&#x20; --availability-zone ap-south-1a

```



\- Name: `public-subnet-a`

\- Subnet ID: `subnet-02a37ae653b93947b`



\---



\## 8. Create Public Subnet B



```bash

aws ec2 create-subnet `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --cidr-block 10.50.2.0/24 `

&#x20; --availability-zone ap-south-1b

```



\- Name: `public-subnet-b`

\- Subnet ID: `subnet-02338d094c28c3f9d`



\---



\## 9. Create Private Subnet A



```bash

aws ec2 create-subnet `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --cidr-block 10.50.11.0/24 `

&#x20; --availability-zone ap-south-1a

```



\- Name: `private-subnet-a`

\- Subnet ID: `subnet-0184cc49b7bb85cfd`



\---



\## 10. Create Private Subnet B



```bash

aws ec2 create-subnet `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --cidr-block 10.50.12.0/24 `

&#x20; --availability-zone ap-south-1b

```



\- Name: `private-subnet-b`

\- Subnet ID: `subnet-090dc15f8fe171e03`



\---



\## 11. Verify All Subnets



```bash

aws ec2 describe-subnets `

&#x20; --filters "Name=vpc-id,Values=vpc-00d0bbdb7cd548a87" `

&#x20; --query "Subnets\[].{Name:Tags\[?Key=='Name']|\[0].Value,CIDR:CidrBlock,AZ:AvailabilityZone,SubnetID:SubnetId,State:State}" `

&#x20; --output table

```



Expected subnets:



\- public-subnet-a

\- public-subnet-b

\- private-subnet-a

\- private-subnet-b



\---



\## 12. Internet Gateway



The Internet Gateway provides a path between the VPC and the public internet.



\*\*Create Internet Gateway\*\*



```bash

aws ec2 create-internet-gateway `

&#x20; --tag-specifications "ResourceType=internet-gateway,Tags=\[{Key=Name,Value=secure-multi-tier-igw}]"

```



\- Internet Gateway ID: `igw-090a2f44b0df3702d`

\- Name: `secure-multi-tier-igw`



\*\*Attach Internet Gateway to VPC\*\*



```bash

aws ec2 attach-internet-gateway `

&#x20; --internet-gateway-id igw-090a2f44b0df3702d `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87

```



\*\*Verify Internet Gateway\*\*



```bash

aws ec2 describe-internet-gateways `

&#x20; --internet-gateway-ids igw-090a2f44b0df3702d `

&#x20; --query "InternetGateways\[0].{ID:InternetGatewayId,Name:Tags\[?Key=='Name']|\[0].Value,Attachments:Attachments\[].{VPC:VpcId,State:State}}" `

&#x20; --output json

```



\---



\## 13. Public Route Table



The public route table controls routing for the public subnets.



\*\*Create Public Route Table\*\*



```bash

aws ec2 create-route-table `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --tag-specifications "ResourceType=route-table,Tags=\[{Key=Name,Value=public-route-table}]"

```



\- Route Table ID: `rtb-0427ff0fb625a0d0d`

\- Name: `public-route-table`



\*\*Add Internet Route\*\*



```bash

aws ec2 create-route `

&#x20; --route-table-id rtb-0427ff0fb625a0d0d `

&#x20; --destination-cidr-block 0.0.0.0/0 `

&#x20; --gateway-id igw-090a2f44b0df3702d

```



Expected: `"Return": true`



\*\*Associate Public Subnet A\*\*



```bash

aws ec2 associate-route-table `

&#x20; --route-table-id rtb-0427ff0fb625a0d0d `

&#x20; --subnet-id subnet-02a37ae653b93947b

```



\*\*Associate Public Subnet B\*\*



```bash

aws ec2 associate-route-table `

&#x20; --route-table-id rtb-0427ff0fb625a0d0d `

&#x20; --subnet-id subnet-02338d094c28c3f9d

```



\---



\## 14. Private Route Table



The private route table controls routing for the private subnets.



\*\*Create Private Route Table\*\*



```bash

aws ec2 create-route-table `

&#x20; --vpc-id vpc-00d0bbdb7cd548a87 `

&#x20; --tag-specifications "ResourceType=route-table,Tags=\[{Key=Name,Value=private-route-table}]"

```



\- Route Table ID: `rtb-0fc0f1e9248dcb5e1`

\- Name: `private-route-table`



\*\*Associate Private Subnet A\*\*



```bash

aws ec2 associate-route-table `

&#x20; --route-table-id rtb-0fc0f1e9248dcb5e1 `

&#x20; --subnet-id subnet-0184cc49b7bb85cfd

```



\*\*Associate Private Subnet B\*\*



```bash

aws ec2 associate-route-table `

&#x20; --route-table-id rtb-0fc0f1e9248dcb5e1 `

&#x20; --subnet-id subnet-090dc15f8fe171e03

```



\---



\## 15. Elastic IP



A public NAT Gateway requires an Elastic IP.



\*\*Allocate Elastic IP\*\*



```bash

aws ec2 allocate-address --domain vpc

```



\- Allocation ID: `eipalloc-089e44d108ff576b7`

\- Public IP: `13.126.221.84`

\- Domain: `vpc`



\---



\## 16. NAT Gateway



The NAT Gateway is deployed in Public Subnet A. Private resources use the NAT Gateway for outbound internet access.



\*\*Create NAT Gateway\*\*



```bash

aws ec2 create-nat-gateway `

&#x20; --subnet-id subnet-02a37ae653b93947b `

&#x20; --allocation-id eipalloc-089e44d108ff576b7 `

&#x20; --tag-specifications "ResourceType=natgateway,Tags=\[{Key=Name,Value=secure-multi-tier-nat}]"

```



NAT Gateway:



| Property | Value |

|---|---|

| Name | secure-multi-tier-nat |

| NAT Gateway ID | nat-03a6309888e96a40e |

| Subnet | subnet-02a37ae653b93947b |

| Public IP | 13.126.221.84 |



\*\*Check NAT Gateway Status\*\*



```bash

aws ec2 describe-nat-gateways `

&#x20; --nat-gateway-ids nat-03a6309888e96a40e `

&#x20; --query "NatGateways\[0].{ID:NatGatewayId,State:State,Subnet:SubnetId,Name:Tags\[?Key=='Name']|\[0].Value}" `

&#x20; --output table

```



The NAT Gateway was verified as: `State: available`



\---



\## 17. Private Route Through NAT Gateway



The private route table requires a default route to the NAT Gateway.



\*\*Create Private Default Route\*\*



```bash

aws ec2 create-route `

&#x20; --route-table-id rtb-0fc0f1e9248dcb5e1 `

&#x20; --destination-cidr-block 0.0.0.0/0 `

&#x20; --nat-gateway-id nat-03a6309888e96a40e

```



Expected: `"Return": true`



\---



\## 18. Verify Private Route



```bash

aws ec2 describe-route-tables `

&#x20; --route-table-ids rtb-0fc0f1e9248dcb5e1 `

&#x20; --query "RouteTables\[0].Routes\[].{Destination:DestinationCidrBlock,Target:NatGatewayId,Gateway:GatewayId,State:State}" `

&#x20; --output table

```



Expected:



```

10.50.0.0/16 → local

0.0.0.0/0    → NAT Gateway

```



\---



\## 19. Verify Complete Routing



```bash

aws ec2 describe-route-tables `

&#x20; --filters "Name=vpc-id,Values=vpc-00d0bbdb7cd548a87" `

&#x20; --query "RouteTables\[].{Name:Tags\[?Key=='Name']|\[0].Value,ID:RouteTableId,Routes:Routes\[].{Destination:DestinationCidrBlock,Gateway:GatewayId,NAT:NatGatewayId,State:State}}" `

&#x20; --output json

```



Expected public route:



```

10.50.0.0/16 → local

0.0.0.0/0    → Internet Gateway

```



Expected private route:



```

10.50.0.0/16 → local

0.0.0.0/0    → NAT Gateway

```



\---



\## 20. Verify Route Table Associations



```bash

aws ec2 describe-route-tables `

&#x20; --filters "Name=vpc-id,Values=vpc-00d0bbdb7cd548a87" `

&#x20; --query "RouteTables\[].{Name:Tags\[?Key=='Name']|\[0].Value,ID:RouteTableId,Associations:Associations\[].SubnetId}" `

&#x20; --output table

```



Expected:



```

public-route-table

&#x20;   subnet-02a37ae653b93947b

&#x20;   subnet-02338d094c28c3f9d



private-route-table

&#x20;   subnet-0184cc49b7bb85cfd

&#x20;   subnet-090dc15f8fe171e03

```



\---



\## 21. Final VPC Verification



```bash

aws ec2 describe-vpcs `

&#x20; --vpc-ids vpc-00d0bbdb7cd548a87 `

&#x20; --query "Vpcs\[0].{VPC:VpcId,CIDR:CidrBlock,State:State,Name:Tags\[?Key=='Name']|\[0].Value}" `

&#x20; --output table

```



Final result:



| Property | Value |

|---|---|

| VPC | vpc-00d0bbdb7cd548a87 |

| Name | secure-multi-tier-vpc |

| CIDR | 10.50.0.0/16 |

| State | available |

