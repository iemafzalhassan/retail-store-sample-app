# Retail Store Sample App - GitOps with EKS Auto Mode

![Banner](./docs/images/banner.png)

<div align="center">
  <div align="center">

[![Stars](https://img.shields.io/github/stars/iemafzalhassan/retail-store-sample-app)](Stars)
![GitHub License](https://img.shields.io/github/license/iemafzalhassan/retail-store-sample-app?color=green)
![Dynamic JSON Badge](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fiemafzalhassan%2Fretail-store-sample-app%2Frefs%2Fheads%2Fmain%2F.release-please-manifest.json&query=%24%5B%22.%22%5D&label=release)


  </div>

  <strong>
  <h2>AWS Containers Retail Sample</h2>
  </strong>
</div>

This is a sample application designed to illustrate various concepts related to containers on AWS. It presents a sample retail store application including a product catalog, shopping cart and checkout, deployed using modern DevOps practices including GitOps and Infrastructure as Code.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [GitOps Workflow](#gitops-workflow)
- [EKS Auto Mode](#eks-auto-mode)
- [Infrastructure Components](#infrastructure-components)
- [CI/CD Pipeline](#cicd-pipeline)
- [Monitoring and Observability](#monitoring-and-observability)
- [Cleanup](#cleanup)
- [Troubleshooting](#troubleshooting)

## Overview

The Retail Store Sample App demonstrates a modern microservices architecture deployed on AWS EKS using GitOps principles. The application consists of multiple services that work together to provide a complete retail store experience:

![Application Architecture Diagram](./docs/images/application-architecture.png)

- **UI Service**: Java-based frontend
- **Catalog Service**: Go-based product catalog API
- **Cart Service**: Java-based shopping cart API
- **Orders Service**: Java-based order management API
- **Checkout Service**: Node.js-based checkout orchestration API

## Infrastructure Architecture

![Infrastructure Architecture Diagram](./docs/images/architecture.png)

The Infrastructure Architecture follows cloud-native best practices:

- **Microservices**: Each component is developed and deployed independently
- **Containerization**: All services run as containers on Kubernetes
- **GitOps**: Infrastructure and application deployment managed through Git
- **Infrastructure as Code**: All AWS resources defined using Terraform
- **CI/CD**: Automated build and deployment pipelines with GitHub Actions

## Prerequisites

Before you begin, ensure you have the following tools installed:

- **AWS CLI** (configured with appropriate credentials)
- **Terraform** (version 1.0.0 or later)
- **kubectl** (compatible with Kubernetes 1.23+)
- **Git** (2.0.0 or later)
- **Docker** (for local development)
- **Helm** 

## Getting Started

Follow these steps to **install Prerequisites:**

- #### 1. AWS CLI:

  * These commands will download and install the **AWS Command Line Interface**.

```sh
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Verify the installation
aws --version
```

- #### 2. Terraform:

  - **Terraform** is installed by downloading the binary appropriate for your operating system.

    - <details>
      <summary><strong>Click for Linux & macOS Instructions</strong></summary>

      1.  **Download the Binary**: Go to the [Terraform Downloads Page](https://releases.hashicorp.com/terraform/1.12.2) to find the correct zip file for your system (e.g., Linux AMD64, macOS ARM64).

      2.  **Install the Binary**: Unzip the file and move the `terraform` executable to a directory in your system's PATH.

        ```sh
        # Example for a downloaded file
        unzip terraform_1.9.0_linux_amd64.zip
        sudo mv terraform /usr/local/bin/
        ```
      3.  **Verify the Installation**:
     
        ```sh
        terraform --version
        ```
      </details>
  
    - <details>
      <summary><strong>Click for Windows Instructions</strong></summary>
  
        * **Official Guide:** [Install Terraform on Windows](https://developer.hashicorp.com/terraform/install)
    
      </details>

- #### 3. kubectl:

  * These commands install a specific version of **kubectl**.

    - <details>
      <summary><strong>Click for macOS Instructions</strong></summary>
  
        ```sh
        # Download the kubectl binary
        curl -LO "https://dl.k8s.io/release/v1.33.3/bin/darwin/arm64/kubectl"

        # Make the binary executable
        chmod +x ./kubectl

          # Move the binary into your PATH
        sudo mv ./kubectl /usr/local/bin/kubectl
        ```

      </details>

    - <details>
      <summary><strong>Click for Linux Instructions</strong></summary>
  
      ```sh
      # Download the kubectl binary
      curl -LO "https://dl.k8s.io/release/v1.33.3/bin/linux/amd64/kubectl"
  
      # Make the binary executable
      chmod +x ./kubectl

      # Move the binary into your PATH
      sudo mv ./kubectl /usr/local/bin/kubectl
      ```
      
      </details>

- #### [4. Docker](https://docs.docker.com/desktop/setup/install/linux/):

  - > **Step 1: Set Up the Repository:**

    ```sh
    sudo apt-get update
    sudo apt-get install \
        ca-certificates \
        curl \
        gnupg
    ```

  - > **Step 2: Add Docker’s Official GPG Key:**

    ```sh
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    ```
  
  - > **Step 3: Set Up the Docker Repository:**

    ```sh
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```


  - > **Step 4: Install Docker Engine:**
    
    ```sh
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    # Verify the installation
    docker --version
    ```

- #### 5. Helm:
  
    ```sh
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh --version v3.18.4
    ```



Follow these steps to deploy the application:

### Step 1. Configure AWS with **`Root User`** Credentials:

  Ensure your AWS CLI is configured with the **Root user credentials:**

```sh
aws configure
```

### Step 2. Clone the Repository:

```sh
git clone https://github.com/iemafzalhassan/retail-store-sample-app.git
cd retail-store-sample-app
```

> [!IMPORTANT]
> ### Step 3: Initial Deployment with Public Images:
>
> <details>
>  <summary><strong>Click to Read:</strong> First-Time Deployment Instructions</summary>
>
>  * On the initial deploy, ArgoCD is configured to use public container images as specified in this guide.
>  * After this first deployment, the GitHub Actions workflow will run automatically, updating the `values.yaml` files to use your private ECR images and tags for all future deployments.
> </details>
> 
> ### Automatic Update Instructions:
> 
> ```sh
> ./scripts/update-helm-values.sh
> ```
> 
> ### Manual Update Instructions:
> 
> For the initial deployment, you must update the `values.yaml` file for each of the five services to use the public ECR images.
> 
> #### **Service-Specific Configuration**
> 
> <details>
> <summary><strong>1. Cart Service</strong></summary>
> 
> Update `src/cart/chart/values.yaml` to match the following content:
> ```yaml
> # ... other values
> image:
>   repository: public.ecr.aws/aws-containers/retail-store-sample-cart
>   pullPolicy: Always
>   tag: "1.2.2"
> # ... other values
> ```
> </details>
> 
> <details>
> <summary><strong>2. Catalog Service</strong></summary>
> 
> Update `src/catalog/chart/values.yaml` to match the following content:
> ```yaml
> # ... other values
> image:
>   repository: public.ecr.aws/aws-containers/retail-store-sample-catalog
>   pullPolicy: Always
>   tag: "1.2.2"
> # ... other values
> ```
> </details>
> 
> <details>
> <summary><strong>3. Checkout Service</strong></summary>
>
> Update `src/checkout/chart/values.yaml` to match the following content:
> ```yaml
> # ... other values
> image:
>   repository: public.ecr.aws/aws-containers/retail-store-sample-checkout
>   pullPolicy: Always
>   tag: "1.2.2"
> # ... other values
> ```
> </details>
> 
> <details>
> <summary><strong>4. Orders Service</strong></summary>
> 
> Update `src/orders/chart/values.yaml` to match the following content:
> ```yaml
> # ... other values
> image:
>   repository: public.ecr.aws/aws-containers/retail-store-sample-orders
>   pullPolicy: Always
>   tag: "1.2.2"
> # ... other values
> ```
> </details>
> 
> <details>
> <summary><strong>5. UI Service</strong></summary>
>
> Update `src/ui/chart/values.yaml` to match the following content:
> ```yaml
> # ... other values
> image:
>   repository: public.ecr.aws/aws-containers/retail-store-sample-ui
>   pullPolicy: Always
>   tag: "1.2.2"
> # ... other values
> ```
> </details>
>
> ### Step 4: Comment out `.github/workflows/update-helm-values.yml`:


### Step 5. Deploy Infrastructure with Terraform:

The deployment is split into two phases for better control:


### Phase 1 of Terraform: Create EKS Cluster 

In Phase 1: Terraform Initialises and creates resources within the retail_app_eks module. 

```sh
cd retail-store-sample-app/terraform/eks/default/
terraform init
terraform apply -target=module.retail_app_eks --auto-approve
```

<img width="1205" height="292" alt="image" src="https://github.com/user-attachments/assets/6f1e407e-4a4e-4a4c-9bdf-0c9b89681368" />


This creates the core infrastructure, including:
- VPC with public and private subnets
- Amazon EKS cluster with Auto Mode enabled
- Bastion host for secure cluster access
- Security groups and IAM roles
  

### Step 6: Update kubeconfig to Access the Amazon EKS Cluster:
```
aws eks update-kubeconfig --name retail-store --region <region>
```

### Phase 2 of Terraform: Once you update kubeconfig, apply the Remaining Configuration:


```bash
terraform apply --auto-approve
```

This deploys:
- ArgoCD for Setup GitOps
- NGINX Ingress Controller
- Cert Manager for SSL certificates

### Step 7: GitHub Actions:

For GitHub Actions, first configure secrets so the pipelines can be automatically triggered:

**Create an IAM User, policies, and generate credentials**

**Go to your GitHub repo → Settings → Secrets and variables → Actions → New repository secret.**


| Secret Name           | Value                              |
|-----------------------|------------------------------------|
| `AWS_ACCESS_KEY_ID`   | `Your AWS Access Key ID`           |
| `AWS_SECRET_ACCESS_KEY` | `Your AWS Secret Access Key`     |
| `AWS_REGION`          | `region-name`                       |
| `AWS_ACCOUNT_ID`        | `your-account-id` |



> [!IMPORTANT]
> Once the entire cluster is created, any changes pushed to the repository will automatically trigger GitHub Actions.

GitHub Actions will automatically build and push the updated Docker images to Amazon ECR.



<img width="2868" height="1130" alt="image" src="https://github.com/user-attachments/assets/f29c3416-d630-4463-81d2-aaa8af9a02da" />


### Verify Deployment

Check if the nodes are running:

```bash
kubectl get nodes
```

### Step 8: Access the Application:

The application is exposed through the NGINX Ingress Controller. Get the load balancer URL:

```bash
kubectl get svc -n ingress-nginx
```

Use the EXTERNAL-IP of the ingress-nginx-controller service to access the application.

<img width="2912" height="1756" alt="image" src="https://github.com/user-attachments/assets/095077d6-d3cb-48f6-b021-e977db5fb242" />

### Step 9: Argo CD Automated Deployment:

**Verify ArgoCD installation**

```
kubectl get pods -n argocd
```


### Step 10: Port-forward to Argo CD UI and login:

**Get ArgoCD admin password**
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
```

**Port-forward to Argo CD UI**
```
kubectl port-forward svc/argocd-server -n argocd 8080:443 &
```

Open your browser and navigate to:
https://localhost:8080

Username: admin 

Password: <output of previous command>



### Argocd UI

<img width="2915" height="1755" alt="image" src="https://github.com/user-attachments/assets/20c00b41-bc87-4038-b06f-56867340a269" />


### Step 9: Monitor application deployment
```
kubectl get pods -n retail-store
kubectl get ingress -n retail-store
```

### Step 11: Cleanup:

To delete all resources created by Terraform:


**For Phase 1: Run this command**

```bash
terraform destroy -target=module.retail_app_eks --auto-approve
```

**For Phase 2: Run this command**
```
terraform destroy --auto-approve
```

<img width="1139" height="439" alt="image" src="https://github.com/user-attachments/assets/5258761a-01c4-49d0-b6f3-997fc10a9f35" />

> [!NOTE]
> Only ECR Repositories you need to Delete it from AWS Console Manually.



## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](./LICENSE) file for details.
