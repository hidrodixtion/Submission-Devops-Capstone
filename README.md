# Capstone Project - Udacity Cloud DevOps Engineer Nanodegree.

## Tasks

- Create EC2 instance and install jenkins
- Install jenkins plugins (BlueOcean, Pipeline:AWS & Docker)
- Deploy clusters using EKS
- Deploy a webapp into cluster via Jenkins pipeline

## Solution

I solved this project using Blue/Green deployment method.

### Flow:

1. Prepare Github repo
2. Launch EC2 instance
3. Install Jenkins & plugins
4. Install `docker`, `awscli`, `kubectl` & `eksctl`
5. Create clusters using `eksctl`
6. Create a (simple) webapp
7. Create Dockerfile
8. Create deployment script (in my sample here: `constroller_blue.json` and `controller_green.json`)
9. Create load balancer deployment script (`lb_service_blue.json` and `lb_service_green.json`)
10. Create Jenkinsfile
11. Prepare credentials in Jenkins credential setting
12. Push all those file (1-10) into the Github repo
13. Connect BlueOcean to the Github repo