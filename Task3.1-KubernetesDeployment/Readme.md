#  Deployment of an Application in a Production Kubernetes Environment

- Here, I have created all the necessary kubernetes resources like Ingress, Service, Deployment, Namespace and pod into a kuberenetes manifest file(sampleapp.yaml) and added my docker image that I  pushed to the docker hub.

# Prerequisites
- kubectl – I have installed this command line tool for working with Kubernetes clusters.

- eksctl – I have installed this command line tool for working with EKS clusters that automates many individual tasks.

- AWS CLI – I have installed this command line tool for working with AWS services, including Amazon EKS and configured my Access and Secret Keys.

- Helm - Helm is the package manager for Kubernetes. It simplifies the management of Kubernetes

_Commands used for creating resources_

- Command used for creating cluster using fargate

        eksctl create cluster --name demo-cluster --region us-east-1 --fargate

- Commands to configure IAM OIDC. It allows us to verify the identity of a user to access AWS resources. This is the reason for creating oicd, where it uses IAM info for providing the access

        export cluster_name=demo-cluster

        oidc_id=$(aws eks describe-cluster --name demo-cluster --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) 

        eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve

- Updating Kubernetes context to access the cluster

        aws eks update-kubeconfig --name demo-cluster --region us-east-1

- Now, I have applied my Application manifest file using below command to create service, ingress, deployment, pod.

        kubectl apply -f sampleapp.yaml

- Created another Fargate Profile for my application using the below command

        eksctl create fargateprofile \
        --cluster demo-cluster \
        --region us-east-1 \
        --name alb-smile-app \
        --namespace smile-app

# ALB Controller

- I have downloaded the alb IAM policy using the below command

        curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

- Created IAM policy and IAM role for alb using below commands

        aws iam create-policy \
        --policy-name AWSLoadBalancerControllerIAMPolicy \
        --policy-document file://iam_policy.json


        eksctl create iamserviceaccount \
        --cluster=demo-cluster \
        --namespace=kube-system \
        --name=aws-load-balancer-controller \
        --role-name AmazonEKSLoadBalancerControllerRole \
        --attach-policy-arn=arn:aws:iam::851725241850:policy/AWSLoadBalancerControllerIAMPolicy \
        --approve

- I have used helm to deploy ALB controller

        helm repo add eks https://aws.github.io/eks-charts

        helm repo update eks

        helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
        --set clusterName=demo-cluster \
        --set serviceAccount.create=false \
        --set serviceAccount.name=aws-load-balancer-controller \
        --set region=us-east-1 \
        --set vpcId=vpc-00aa5398de6563ca9

- After configuration, these are the commands that I used to verify the pods. 



        kubectl get deploy -n kube-system
        NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
        aws-load-balancer-controller   2/2     2            2           77s
        coredns                        2/2     2            2           6h12m

        kubectl get deploy -n smile-app
        NAME               READY   UP-TO-DATE   AVAILABLE   AGE
        deployment-smile   5/5     5            5           17h

        kubectl get pods -n kube-system
        NAME                                           READY   STATUS    RESTARTS   AGE
        aws-load-balancer-controller-b99f576cf-5np7b   1/1     Running   0          2m42s
        aws-load-balancer-controller-b99f576cf-ktgxz   1/1     Running   0          2m42s
        coredns-fdb66fb7c-c99rq                        1/1     Running   0          6h3m
        coredns-fdb66fb7c-fbscr                        1/1     Running   0          6h3m

        kubectl get svc -n smile-app
        NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        service-smile   NodePort   10.100.45.19   <none>        8000:32370/TCP   17h


        kubectl get ingress -n smile-app
        NAME            CLASS    HOSTS   ADDRESS                                          
        ingress-smile   alb       *       k8s-sampleapp-ingress2-e1d9d1f206-1078327551.us-east-1.
                                         elb.amazonaws.com          
        PORTS   AGE
        80      14m

- Now, with the ALB load balancer link, I was able to access the application on my browser.

_Scalability & Availability:_

- I have used Fargate which automatically scales based on the demand. It also provides high availability by distributing load across multiple Availability Zones.
- Besides, I have kept the replica count of 5 in kubernetes service to make the application highly available.


