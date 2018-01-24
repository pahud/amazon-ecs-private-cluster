# amazon-ecs-private-cluster

This reference architecture is aimed to build Amazon ECS workload in private subnet with restricted internet access. The container instances in private subnet will be restricted to access Amazon ECR and Amazon ECS Service Endpoint only through Squid Proxy, which is running in Amazon ECS cluster within public subnets.

Customer can leverage this design to make sure the container instances within private subnets can only have moderated internet access while still provide public services(i.e. Nginx) for requests coming from public internet through public ALB(Application Load Balancer).



## HOWTO

1. prepare and push the nginx and squid docker images to ECR

   ```
   // create ECR repos with aws-cli
   $ aws --region ap-northeast-1 ecr create-repository --repository-name nginx
   $ aws --region ap-northeast-1 ecr create-repository --repository-name squid
   // docker pull the official nginx docker image
   $ docker pull nginx
   // login ECR and push image to ECR
   $ aws ecr get-login --no-include-email --region ap-northeast-1 | bash
   $ docker tag nginx:latest <YOUR_AWS_ACCOUNT>.dkr.ecr.ap-northeast-1.amazonaws.com/nginx:latest
   $ docker push <YOUR_AWS_ACCOUNT>.dkr.ecr.ap-northeast-1.amazonaws.com/nginx:latest
   // build the squid image
   $ cd squid
   $ docker build -t squid:latest .
   $ docker tag squid:latest <YOUR_AWS_ACCOUNT>.dkr.ecr.ap-northeast-1.amazonaws.com/squid:latest
   // push to ECR
   $ docker push <YOUR_AWS_ACCOUNT>.dkr.ecr.ap-northeast-1.amazonaws.com/squid:latest
   ```

   ​

2. click the button below to provision the stack in Tokyo(ap-northeast-1)

[![cloudformation-launch-stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/new?stackName=private-ecs-demo&templateURL=https://s3-us-west-2.amazonaws.com/pahud-cfn-us-west-2/amazon-ecs-private-cluster/cloudformation/service.yml)

3. click the **DemoURL** in the cloudformation output tab when the stack  is completedly launched. You should be able to see the Nginx welcome page.

### Architecture Diagram

![diagram](diagram.png)