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
17. #Infrastructure 
18. clone the repository:
git clone https://github.com/your-username/k8s-stateless-app-deployment.git
Navigate to Terraform directory: cd terraform
Generate SSH key pair: ssh-keygen -t rsa -f ass1-prod -q -N ""
Update variables.tf with key name and configure S3 backend in config.tf.
Apply Terraform: terraform init terraform validate terraform plan terraform apply -auto-approve
SSH into EC2 instance: chmod 400 ass1-prod ssh -i ass1-prod ec2-user@
Run GitHub Workflow to build and push Docker images to ECR.
