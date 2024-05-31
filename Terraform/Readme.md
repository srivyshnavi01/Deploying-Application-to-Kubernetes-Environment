# Terraform for AWS Resources

- I have added all the necessary files that are used for creating the resources inside AWS.

# Execution of Terraform Code.

- Used the below commands for creating the Infrastructure.

Terraform Init: It helps in downloading all the required Provider Configuration

            terraform init

Terraform Plan: It helps in Creating an execution plan that shows what Terraform will do when we apply our configuration.

            terraform plan

Terraform Apply: I have used the terraform apply command to create the required resources inside the AWS

            terraform apply





CORE DNS Issue:

I have used below command to resolve "_no nodes available to schedule pods for core dns_"

        kubectl patch deployment coredns \
        -n kube-system \
        --type json \
        -p='[{"op": "remove", "path": "/spec/template/metadata/annotations/eks.amazonaws.com~1compute-type"}]'



# Security:

- I have used S3 Backend for storing the terraform state file and used Dynamo DB for state locking when a remote backend is configured. It ensures that only one user or process can modify the Terraform state at a timestatelocking. By this approach, we can mitigate the disadvantage of storing sensitive information in version control systems and ensuring safe concurrent access to our infrastructure.

- I have used ALB ingress controller infront of my Application which helps in secure communication between clients and the load balancer.

- I have created NAT gateway which enables instances in a private subnet to connect to the internet.

- Also by using IAM Roles and Policies I have securely created the permissions for accessing the AWS Resources.




_Additional Notes:_

If I had more time, I would have done the below 
- I would have worked on implementing Metrics for Infrastructure and Application.

- Moreover, I would have implemented Certificates from AWS certifcate Manager to secure my Application.
