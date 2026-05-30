# 📝 Full-Stack Blogging Application

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.2-brightgreen.svg?style=flat-square&logo=spring)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-17-orange.svg?style=flat-square&logo=openjdk)](https://www.oracle.com/java/)
[![Docker](https://img.shields.io/badge/Docker-Enabled-blue.svg?style=flat-square&logo=docker)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-blue.svg?style=flat-square&logo=kubernetes)](https://kubernetes.io/)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-purple.svg?style=flat-square&logo=terraform)](https://www.terraform.io/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-red.svg?style=flat-square&logo=jenkins)](https://www.jenkins.io/)

A complete, production-ready Full-Stack Blogging / Microblogging web application built with **Spring Boot** and **Thymeleaf**. This repository is configured with a robust, enterprise-grade DevOps lifecycle featuring **Docker containerization**, **Jenkins pipelines**, **Terraform Infrastructure as Code (IaC)** for provisioning **AWS EKS**, and **Kubernetes** manifests for container orchestration.

---

## 🚀 Key Features

*   **User Registration & Authentication**: Complete Register, Login, and Logout flows powered by **Spring Security** using `BCryptPasswordEncoder` for secure credentials storage.
*   **Microblogging Feed**: Authenticated users can publish blog posts/tweets, which are dynamically linked to their profiles and displayed chronologically on a shared dashboard.
*   **In-Memory Database**: Uses **H2 Database** for rapid local testing and development with an interactive dashboard console.
*   **Multi-Stage Containerization**: Custom lightweight Docker image utilizing Eclipse Temurin JDK 17 Alpine.
*   **Infrastructure as Code (IaC)**: Automated AWS EKS cluster deployment using modular **Terraform** configurations including custom VPC, public subnets, internet gateways, route tables, IAM roles, and security groups.
*   **Container Orchestration**: Kubernetes Deployment (scaled to 2 replicas) and a LoadBalancer Service to route traffic securely.
*   **CI/CD Pipeline**: Fully automated build, test, and package pipelines with **Jenkinsfile** (runs on default agent or dedicated worker label).

---

## 🛠️ Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Backend Framework** | Spring Boot 3.3.2 (Web, Security, JPA, Test) |
| **Database & ORM** | H2 In-Memory Database, Hibernate JPA |
| **Frontend Templates**| Thymeleaf, HTML5, CSS3, Bootstrap |
| **Security** | Spring Security 6 (Custom UserDetailsService, BCrypt) |
| **Build Tool** | Maven 3.x |
| **CI/CD** | Jenkins |
| **Infrastructure** | AWS EKS, AWS VPC, AWS IAM, Terraform |
| **Containerization** | Docker, Kubernetes |

---

## 📂 Project Structure

```text
├── EKS_Terraform/              # Terraform configurations for AWS EKS
│   ├── main.tf                 # VPC, EKS Cluster, Node Group, and IAM setups
│   ├── variables.tf            # Input variables (e.g. SSH keys)
│   └── output.tf               # Outputs (e.g. Cluster Endpoint)
├── src/
│   ├── main/
│   │   ├── java/com/example/twitterapp/
│   │   │   ├── config/         # Spring Security & Custom UserDetails Service
│   │   │   ├── controller/     # Controllers for Post feed & Authentication
│   │   │   ├── model/          # User & Post JPA Entities
│   │   │   ├── repository/     # Spring Data JPA Repositories
│   │   │   ├── service/        # Business logic Services & Implementations
│   │   │   └── TwitterAppApplication.java  # Main application entrypoint
│   │   └── resources/
│   │       ├── templates/      # Thymeleaf HTML Templates (home, add, login, register)
│   │       └── application.properties # H2 & Thymeleaf configurations
├── Dockerfile                  # Containerization instructions
├── Jenkinsfile                 # CI/CD pipeline script
├── Jenkinsfile1                # CI/CD pipeline script with slave label
├── deployment-service.yml      # Kubernetes Deployment & Service Manifests
└── pom.xml                     # Maven dependencies & build configurations
```

---

## ⚙️ Local Development Guide

### Prerequisites
Make sure you have the following installed on your machine:
*   [Java Development Kit (JDK) 17](https://adoptium.net/temurin/releases/)
*   [Apache Maven 3.x](https://maven.apache.org/)
*   [Docker Desktop](https://www.docker.com/products/docker-desktop/) (optional, for local container runs)

### Steps to Run Locally

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/ankit-tiwari-01/full-stack-blogApp.git
   cd full-stack-blogApp
   ```

2. **Build and Test the Application:**
   Run Maven package to run unit tests and bundle the application into an executable `.jar` file:
   ```bash
   mvn clean package
   ```

3. **Run the Application:**
   Launch the Spring Boot development server:
   ```bash
   mvn spring-boot:run
   ```

4. **Access the Application:**
   *   **Main Application Webpage**: Open [http://localhost:8080](http://localhost:8080) in your browser.
   *   **H2 Database Console**: Open [http://localhost:8080/h2-console](http://localhost:8080/h2-console)
       *   **JDBC URL**: `jdbc:h2:mem:twitterapp`
       *   **Username**: `sa`
       *   **Password**: `password`

---

## 🐳 Docker Deployment

To build a Docker container and run the blogging application locally:

1. **Build the Docker Image:**
   ```bash
   docker build -t full-stack-blogapp:latest .
   ```

2. **Run the Docker Container:**
   ```bash
   docker run -d -p 8080:8080 --name blogapp full-stack-blogapp:latest
   ```

---

## ☁️ Production Cloud Deployment (AWS EKS)

The project includes ready-to-use Terraform scripts and Kubernetes manifests to provision and deploy the application to AWS.

### Step 1: Provision Infrastructure with Terraform
1. Navigate to the Terraform directory:
   ```bash
   cd EKS_Terraform
   ```
2. Initialize Terraform and install providers:
   ```bash
   terraform init
   ```
3. Plan and apply the infrastructure changes:
   ```bash
   terraform apply -auto-approve
   ```
   *This provisions a VPC, Subnets, Security Groups, IAM Roles, and an AWS EKS Cluster with 3 worker nodes (t2.large).*

### Step 2: Deploy to Kubernetes
1. Configure `kubectl` to interact with your new AWS EKS cluster:
   ```bash
   aws eks update-kubeconfig --region ap-south-1 --name devopsshack-cluster
   ```
2. Apply the Kubernetes Deployment & LoadBalancer Service:
   ```bash
   kubectl apply -f ../deployment-service.yml
   ```
3. Retrieve the public LoadBalancer DNS endpoint to access the application:
   ```bash
   kubectl get service bloggingapp-ssvc
   ```

---

## 🔄 CI/CD Pipelines with Jenkins

The repository contains two Jenkinsfiles (`Jenkinsfile` and `Jenkinsfile1`) to build automation pipelines.

*   **Jenkinsfile**: Standard declarative pipeline that executes on any available agent.
*   **Jenkinsfile1**: Configured to target specific Jenkins workers with the `slave-1` label.

**Pipeline Stages:**
1.  **Compile**: Runs `mvn compile` to verify syntax and source code.
2.  **Test**: Runs `mvn test` to execute JUnit unit tests and generate test coverage reports via **JaCoCo**.
3.  **Package**: Bundles the application using `mvn package` to generate the executable jar.

---

## 🔒 Security Specifications

*   **Public Access**: `/register`, `/home`, `/h2-console/**`, and `/images/**` are accessible by default.
*   **Protected Access**: Any request to add posts (`/add`) or view the central feed (`/`) requires authentication. Unauthenticated users are automatically redirected to `/login`.
*   **Session Management**: Custom logout handler invalidates user HTTP sessions and clears credentials context before redirecting to `/login?logout`.
