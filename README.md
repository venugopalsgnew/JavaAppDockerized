

**How to make authenticate my k8 cluster with AWS ECR registry**

Install AWS CLI and do aws configure to have access to ECR registry

To authenticate your Kubernetes cluster with AWS ECR (Elastic Container Registry), you need to create a Kubernetes secret containing the necessary credentials to pull images from ECR. Here's how you can do it:

Get ECR Login Command: Use the AWS CLI to generate an authentication token for ECR. Run the following command:

We need to install aws cli on k8 master and configure with ecr full access

aws ecr get-login-password --region <AWS_REGION> | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com

aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 287489840086.dkr.ecr.ap-southeast-1.amazonaws.com

Copy $HOME/.docker/config.json to /home/ubuntu/.docker/config.json 


Replace <AWS_REGION> with your AWS region and <AWS_ACCOUNT_ID> with your AWS account ID.


Create Kubernetes Secret: Now, you need to create a Kubernetes secret containing the ECR credentials. Run the following command:

kubectl create secret generic ecr-registry \
    --from-file=.dockerconfigjson=$HOME/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson


This command creates a Kubernetes secret named ecr-registry using the Docker configuration file stored at $HOME/.docker/config.json.
Reference Secret in Deployment: In your deployment YAML file, reference the Kubernetes secret you created in the imagePullSecrets field. For example:


kubectl get secret -- we can verify secret name 

**deploy.yaml**


apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: <ECR_REGISTRY>/<IMAGE_NAME>:<TAG>
      imagePullSecrets:
        - name: ecr-registry

        
Replace <ECR_REGISTRY> with your ECR registry URL, <IMAGE_NAME> with the name of your Docker image, and <TAG> with the desired tag.
With these steps, your Kubernetes cluster will be authenticated to pull images from AWS ECR using the provided credentials. Make sure to keep your credentials secure and rotate them regularly for security best practices.

