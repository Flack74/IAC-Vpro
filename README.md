# 🚀 VProfile EKS Infrastructure as Code

[![Terraform](https://img.shields.io/badge/Terraform-v1.6.6+-623CE4?style=flat&logo=terraform&logoColor=white)](https://terraform.io)
[![AWS](https://img.shields.io/badge/AWS-EKS-FF9900?style=flat&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/eks/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.27-326CE5?style=flat&logo=kubernetes&logoColor=white)](https://kubernetes.io)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?style=flat&logo=github-actions&logoColor=white)](https://github.com/features/actions)

A production-ready Infrastructure as Code (IaC) solution for deploying a scalable Amazon EKS cluster with automated CI/CD pipeline using Terraform and GitHub Actions.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Infrastructure Components](#infrastructure-components)
- [CI/CD Pipeline](#cicd-pipeline)
- [Configuration](#configuration)
- [Monitoring & Security](#monitoring--security)
- [Cost Optimization](#cost-optimization)
- [Contributing](#contributing)

## 🎯 Overview

This project demonstrates enterprise-level Infrastructure as Code practices by provisioning a complete Amazon EKS (Elastic Kubernetes Service) environment on AWS. The infrastructure is designed with scalability, security, and cost-efficiency in mind, featuring automated deployment through GitHub Actions.

**Key Highlights:**
- 🏗️ **Infrastructure as Code**: Complete AWS EKS cluster provisioning using Terraform
- 🔄 **Automated CI/CD**: GitHub Actions workflow for continuous deployment
- 🛡️ **Security First**: Private subnets, security groups, and IAM roles following AWS best practices
- 📈 **Auto Scaling**: Managed node groups with automatic scaling capabilities
- 💰 **Cost Optimized**: t3.small instances with efficient resource allocation

## 🏛️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        AWS Cloud                             │
│  ┌────────────────────────────────────────────────────────┐  │
│  │                VPC (172.20.0.0/16)                     │  │
│  │                                                        │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │  │
│  │  │ Public Subnet│  │ Public Subnet│  │ Public Subnet│  │  │
│  │  │   AZ-1       │  │   AZ-2       │  │   AZ-3       │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘  │  │
│  │         │                  │                  │        │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │  │
│  │  │Private Subnet│  │Private Subnet│  │Private Subnet│  │  │
│  │  │   EKS Nodes  │  │   EKS Nodes  │  │   EKS Nodes  │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘  │  │
│  │                                                        │  │
│  │              EKS Control Plane                         │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

## ✨ Features

### Infrastructure Features
- **🌐 Multi-AZ VPC**: High availability across 3 availability zones
- **🔒 Private EKS Nodes**: Worker nodes deployed in private subnets for enhanced security
- **🚪 NAT Gateway**: Single NAT gateway for cost optimization
- **📊 Auto Scaling**: Managed node groups with configurable scaling policies
- **🏷️ Resource Tagging**: Comprehensive tagging strategy for resource management

### DevOps Features
- **🔄 GitOps Workflow**: Infrastructure changes triggered by Git commits
- **✅ Automated Testing**: Terraform validation, formatting, and planning
- **🚀 Zero-Downtime Deployment**: Automated apply on main branch
- **📦 State Management**: Remote state storage in S3 with locking
- **🎛️ Environment Separation**: Support for multiple environments (main/stage)

## 🛠️ Prerequisites

- **AWS Account** with appropriate permissions
- **Terraform** >= 1.6.6
- **AWS CLI** configured
- **kubectl** for cluster management
- **GitHub repository** with secrets configured

### Required AWS Permissions
- EKS cluster creation and management
- VPC and networking resources
- IAM roles and policies
- EC2 instances and security groups

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/Flack74/IAC-Vpro.git
cd IAC-Vpro
```

### 2. Configure GitHub Secrets
Set up the following secrets in your GitHub repository:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `BUCKET_TF_STATE` (S3 bucket for Terraform state)

### 3. Customize Variables
Edit `terraform/variables.tf` to match your requirements:
```hcl
variable "region" {
  default = "us-east-1"  # Change to your preferred region
}

variable "clusterName" {
  default = "vprofile-eks74"  # Customize cluster name
}
```

### 4. Deploy Infrastructure
Push changes to the `main` branch to trigger automatic deployment:
```bash
git add .
git commit -m "Deploy EKS infrastructure"
git push origin main
```

## 🏗️ Infrastructure Components

### VPC Configuration
- **CIDR Block**: 172.20.0.0/16
- **Public Subnets**: 3 subnets across different AZs
- **Private Subnets**: 3 subnets for EKS worker nodes
- **Internet Gateway**: For public subnet internet access
- **NAT Gateway**: Single gateway for cost optimization

### EKS Cluster
- **Kubernetes Version**: 1.27
- **Node Groups**: 2 managed node groups
  - **Group 1**: 1-3 t3.small instances (desired: 2)
  - **Group 2**: 1-2 t3.small instances (desired: 1)
- **AMI Type**: Amazon Linux 2 (AL2_x86_64)

### Security Features
- Private subnets for worker nodes
- Security groups with least privilege access
- IAM roles following AWS best practices
- Cluster endpoint accessible from public internet (configurable)

## 🔄 CI/CD Pipeline

The GitHub Actions workflow (`terraform.yml`) provides:

### Trigger Conditions
- **Push** to `main` or `stage` branches
- **Pull Request** to `main` branch
- **Path filtering**: Only triggers on `terraform/**` changes

### Pipeline Stages
1. **🔍 Code Checkout**: Retrieves latest code
2. **⚙️ Terraform Setup**: Installs Terraform CLI
3. **🔧 Initialize**: Configures backend and providers
4. **📝 Format Check**: Ensures code formatting standards
5. **✅ Validation**: Validates Terraform configuration
6. **📋 Planning**: Creates execution plan
7. **🚀 Apply**: Deploys infrastructure (main branch only)
8. **🎛️ Kubectl Config**: Configures cluster access
9. **🌐 Ingress Setup**: Installs NGINX ingress controller

## ⚙️ Configuration

### Environment Variables
```yaml
AWS_REGION: us-east-1
EKS_CLUSTER: vprofile-eks74
BUCKET_TF_STATE: vprofileactions74
```

### Terraform Backend
```hcl
backend "s3" {
  bucket = "vprofileactions74"
  key    = "terraform.tfstate"
  region = "us-east-1"
}
```

### Customization Options
- **Instance Types**: Modify in `eks-cluster.tf`
- **Scaling Policies**: Adjust min/max/desired sizes
- **Network CIDR**: Update VPC and subnet ranges
- **Kubernetes Version**: Change cluster version

## 🛡️ Monitoring & Security

### Security Best Practices Implemented
- ✅ Private subnets for worker nodes
- ✅ IAM roles with minimal required permissions
- ✅ Security groups with restricted access
- ✅ Encrypted communication between components
- ✅ Regular security updates through managed node groups

### Monitoring Capabilities
- CloudWatch integration for cluster metrics
- EKS control plane logging
- Node group health monitoring
- Resource utilization tracking

## 💰 Cost Optimization

### Implemented Strategies
- **Single NAT Gateway**: Reduces NAT gateway costs
- **t3.small Instances**: Cost-effective compute resources
- **Auto Scaling**: Scales down during low usage
- **Spot Instances**: Can be configured for additional savings
- **Resource Tagging**: Enables cost allocation and tracking

### Estimated Monthly Cost
- **EKS Control Plane**: ~$73/month
- **Worker Nodes**: ~$30-60/month (depending on usage)
- **Networking**: ~$45/month (NAT Gateway)
- **Total**: ~$150-180/month

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙋‍♂️ Support

For questions or support, please open an issue in the GitHub repository.

---

**Built with ❤️ using Terraform, AWS EKS, and GitHub Actions**

*This project demonstrates production-ready Infrastructure as Code practices suitable for enterprise environments.*
