# k8s-stateless-app-deployment
In this project we host a containerized application in the locally simulated single-node K8s cluster. We host the containerized application on kind cluster running on Amazon Linux-based EC2 instance in AWS environment. 
# Prerequisites
kind and kubectl installed.
AWS CLI configured with permissions.
SSH key pair for EC2 access.
# Assignment flow
1.	Deploy Amazon Linux based EC2 with sufficient capacity to run kind cluster and host our containerized application
2.	Install all the pre-requisites on the Amazon EC2 needed to host the containerized application on K8s cluster created by kind (kind, kubectl).
3.	Create K8s cluster using kind tool.
4.	Deploy containerized application using pod, replicaset , deployment and service manifests.
5.	Expose web application using Service of type Nodeport
6.	Expose MySQL using Service of type ClusterIP
7.	Update the applications and deploy the new version of the application
## Infrastructure Setup
1. Clone repository:
   git clone https://github.com/Arpit-commits/Arpit-CLO835-Assignment2.git
   cd Arpit-CLO835-Assignment2
2. Navigate to Terraform directory:
   cd terraform
3. Generate SSH key pair:
   ssh-keygen -t rsa -f vidhi-dev -q -N ""
4. Update `variables.tf` with key name and configure S3 backend in `config.tf`.
5. Apply Terraform:
   terraform init
   terraform validate
   terraform plan
   terraform apply -auto-approve
6. SSH into EC2 instance:
   chmod 400 vidhi-dev
   ssh -i vidhi-dev ec2-user@<EC2-Instance-IP>
7. Run GitHub Workflow to build and push Docker images to ECR.
## AWS CLI and ECR Setup
1. Configure AWS credentials:
   vi ~/.aws/credentials  # Add credentials
   vi ~/.aws/config       # Set region
2. Authenticate Docker with ECR:
   aws ecr get-login-password --region <AWS_REGION> | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/<ECR_REPO_NAME>
3. Verify ECR images:
   aws ecr list-images --repository-name <ECR_REPO_NAME> --region <AWS_REGION>
   ## Kubernetes Cluster Setup
1. Create Kind cluster:
   chmod +x ./init_kind.sh
   ./init_kind.sh
2. Update PATH if needed:
   echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
   source ~/.bashrc
   ### Windows Setup:
1. Install `kind`:
   curl -Lo ./kind.exe https://kind.sigs.k8s.io/dl/v0.26.0/kind-windows-amd64
2. Install `kubectl`:
   curl -LO https://dl.k8s.io/release/v1.29.13/bin/windows/amd64/kubectl.exe
3. Add both to PATH, restart terminal, and verify:
   kind version
## Application Deployment

 ### MySQL
1. Create namespace:
   kubectl create namespace mysql-ns
2. Create ECR secret:
   aws ecr get-login-password --region <AWS_REGION> | kubectl create secret docker-registry ecr-secret \
     --docker-server=<AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com \
     --docker-username=AWS \
     --docker-password=$(aws ecr get-login-password --region <AWS_REGION>) \
     --namespace=mysql-ns
3. Deploy MySQL:
   kubectl apply -f mysql/pod.yaml
   kubectl apply -f mysql/service.yaml
4. Verify:
   kubectl get pods -n mysql-ns
   kubectl get svc -n mysql-ns
   kubectl version --client
### Web Application
1. Create namespace:
   kubectl create namespace web-ns
2. Create ECR secret:
   aws ecr get-login-password --region <AWS_REGION> | kubectl create secret docker-registry ecr-secret \
     --docker-server=<AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com \
     --docker-username=AWS \
     --docker-password=$(aws ecr get-login-password --region <AWS_REGION>) \
     --namespace=web-ns
3. Deploy web app:
   kubectl apply -f web/pod.yaml
   kubectl apply -f web/service.yaml
4. Verify:
   kubectl get pods -n web-ns
   kubectl get svc -n web-ns

## Rolling Updates
1. Update `web/deployment.yaml` (e.g., new image, APP_COLOR=pink):
   image: <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/<ECR_REPO_NAME>:my_app_v2
   env:
   - name: APP_COLOR
     value: "pink"
2. Apply update:
   kubectl apply -f web/deployment.yaml
3. Check rollout:
   kubectl rollout status deployment/web-deployment -n web-ns
   kubectl get pods -n web-ns
4. Test updated app:
   curl http://localhost:30000/

## Debugging
- Check logs:
   kubectl logs -n web-ns pod/web-pod
- Scale replicas:
   kubectl scale --replicas=6 rs/web-rs -n web-ns
- Verify services:
   kubectl get svc -A

## Conclusion
This project provided practical experience with Kubernetes, covering cluster setup, application deployment, and rolling updates. Troubleshooting enhanced my understanding of container orchestration.
## links
video:
