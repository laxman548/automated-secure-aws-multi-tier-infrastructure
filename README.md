\# Automated Secure AWS Multi-Tier Infrastructure



A hands-on Cloud and DevOps project focused on designing, deploying, securing, and automating a real-world multi-tier application infrastructure on AWS.



\---



\## 🎯 Project Objective



Design and deploy a secure AWS infrastructure where:



\- Users can access the application from the internet.

\- Public access is controlled through a load balancer.

\- Application servers remain inside private subnets.

\- Sensitive data remains inside a private database.

\- Infrastructure is gradually automated using Terraform.

\- Deployment and operational tasks are automated using DevOps tools.



The project is being built step by step, starting with AWS networking and gradually introducing EC2, load balancing, Docker, Terraform, CI/CD, monitoring, and Kubernetes.



\---



\## 🏗️ Planned Architecture



```text

&#x20;                        INTERNET

&#x20;                            |

&#x20;                            v

&#x20;                 +----------------------+

&#x20;                 |   Public Load        |

&#x20;                 |      Balancer        |

&#x20;                 +----------+-----------+

&#x20;                            |

&#x20;                  +---------+---------+

&#x20;                  |                   |

&#x20;                  v                   v

&#x20;         +----------------+   +----------------+

&#x20;         | App Server 1   |   | App Server 2   |

&#x20;         |    PRIVATE     |   |    PRIVATE     |

&#x20;         +-------+--------+   +-------+--------+

&#x20;                 |                    |

&#x20;                 +---------+----------+

&#x20;                           |

&#x20;                           v

&#x20;                  +------------------+

&#x20;                  |     Database     |

&#x20;                  |     PRIVATE      |

&#x20;                  +------------------+

