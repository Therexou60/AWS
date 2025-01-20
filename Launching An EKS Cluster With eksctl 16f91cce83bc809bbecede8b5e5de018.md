# Launching An EKS Cluster With eksctl

## Introduction

You are a DevOps engineer who has been tasked with creating a test Elastic Kubernetes Service cluster, as well as ensuring `kubectl`
 functions as expected when utilizing EKS. You've been provided with 
test Service and Deployment definitions and a pre-configured EC2 
instance to serve as the EKS admin interface for your environment.

In this lab, you will work with the AWS command-line interface and console, using command-line utilities like `eksctl` and `kubectl` to launch an EKS cluster, provision a Kubernetes Deployment and Pod running instances of `nginx`, and create a `LoadBalancer` Service to expose your application over the internet.

## Solution

Log in to the eksctl server instance using the credentials provided:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>

```

### Provision an EKS Cluster

1. Check that `eksctl` and `kubectl` are correctly installed:
    
    ```
    eksctl version
    kubectl version --short --client
    
    ```
    
2. Create an EKS cluster with three worker nodes in `us-east-1`:
    
    > Note: Use Kubernetes version 1.16 or later.
    > 
    
    ```
    eksctl create cluster --name dev --version 1.16 --region us-east-1 --nodegroup-name standard-workers --node-type t3.micro --nodes 3 --nodes-min 1 --nodes-max 4 --managed
    
    ```
    
3. Log in to the AWS Management Console using the provided lab credentials.
4. From *Services*, select **CloudFormation**.
5. Select the `eksctl-dev-cluster` being created.
6. Select the *Events* tab and view the `CREATE_FAILED` error status messages.
7. Click **Stacks** to return to the main *Stacks* page.
8. Select the `eksctl-dev-cluster` and click **Delete** > **Delete stack**.
9. Run the command again to create the EKS cluster:
    
    > Note: Provisioning the EKS cluster may take approximately 15-20 minutes.
    > 
    
    ```
    eksctl create cluster --name dev --version 1.16 --region us-east-1 --nodegroup-name standard-workers --node-type t3.micro --nodes 3 --nodes-min 1 --nodes-max 4 --managed
    
    ```
    

### Create a Deployment on Your EKS Cluster

1. View the newly created `dev` EKS cluster:
    
    ```
    eksctl get cluster
    
    ```
    
2. Enable kubectl to speak to the cluster:
    
    ```
    aws eks update-kubeconfig --name dev --region us-east-1
    
    ```
    
3. Clone the [EKS Basics](https://github.com/ACloudGuru-Resources/Course_EKS-Basics) GitHub repo:
    
    ```
    git clone https://github.com/ACloudGuru-Resources/Course_EKS-Basics
    
    ```
    
4. Move into the `Course_EKS_Basics` directory:
    
    ```
    cd Course_EKS_Basics/
    
    ```
    
5. Create an NGINX `LoadBalancer` Service:
    
    ```
    kubectl apply -f ./nginx-svc.yaml
    
    ```
    
6. Check the status of the Service:
    
    ```
    kubectl get service
    
    ```
    
7. Copy the external IP for later use.
8. Create an NGINX Deployment:
    
    ```
    kubectl apply -f ./nginx-deployment.yaml
    
    ```
    
9. Check the status of the Deployment, Pods, ReplicaSets, and nodes:
    
    ```
    kubectl get deployment
    kubectl get pod
    kubectl get rs
    kubectl get node
    
    ```
    
10. Curl the external IP of the NGINX `LoadBalancer` Service external IP:
    
    ```
    curl <NGINX_SERVICE_EXTERNAL_IP>
    
    ```
    

### Test the High Availability Features of Your EKS Cluster

1. From the AWS Management Console, navigate to EC2.
2. Under *Resources*, click **Instances (running)**.
3. Select the three `t3.micro` cluster instances and click **Instance state** > **Terminate instance** > **Terminate**.
4. Return to the terminal window and curl the NGINX `LoadBalancer` again:
    
    ```
    curl <NGINX_SERVICE_EXTERNAL_IP>
    
    ```
    
    You should receive a message `Empty reply from server`.
    
5. Return to the AWS console and navigate to CloudFormation.
6. Select the `eksctl-dev-nodegroup-standard-workers` stack.
7. Select the *Events* tab and view the status messages. The worker nodes should be replaced without any intervention required.
    
    > Note: It may take several minutes for the new instances to be provisioned.
    > 
8. Return to the terminal window and check the status of the configuration:
    
    ```
    kubectl get pod
    kubectl get node
    kubectl get service
    kubectl get deployment
    
    ```
    
9. Run the curl command again:
    
    ```
    curl <NGINX_SERVICE_EXTERNAL_IP>
    
    ```
    

## Conclusion

Congratulations — you've completed this hands-on lab!