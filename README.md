\# Automated Secure AWS Multi-Tier Infrastructure



\## Project Objective



Design, deploy, secure, and automate a real-world AWS application infrastructure where users can access the application through a controlled public entry point while application servers and sensitive data remain protected inside private networks.



\## Simple Architecture



Internet

&#x20;  |

&#x20;  v

Public Load Balancer

&#x20;  |

&#x20;  +-------------------+

&#x20;  |                   |

&#x20;  v                   v

Private App Server 1  Private App Server 2

&#x20;  |                   |

&#x20;  +---------+---------+

&#x20;            |

&#x20;            v

&#x20;      Private Database

All components will be deployed inside an AWS VPC.



\## Network Design



VPC:

10.50.0.0/16



Public Subnet A:

10.50.1.0/24



Public Subnet B:

10.50.2.0/24



Private Subnet A:

10.50.11.0/24



Private Subnet B:

10.50.12.0/24



\## Technologies



\- AWS

\- Linux

\- Networking

\- AWS CLI

\- EC2

\- VPC

\- Load Balancing

\- Docker

\- Terraform

\- GitHub

\- GitHub Actions

\- CloudWatch

\- Kubernetes



\## Project Phases



1\. GitHub and project documentation

2\. AWS CLI setup

3\. IAM setup

4\. VPC and networking

5\. EC2

6\. Security

7\. Load Balancing

8\. Docker

9\. Terraform

10\. CI/CD

11\. Monitoring

12\. Kubernetes



\## Current Status



Project initialization.

