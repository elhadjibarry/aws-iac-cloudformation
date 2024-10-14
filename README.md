# AWS CloudFormation Template for VPC, EC2, and ALB

This CloudFormation template deploys a robust network architecture on AWS following the best practice. It includes a VPC, subnets, NAT gateways, EC2 instances, and an Application Load Balancer (ALB). The template also sets up the necessary security groups for the EC2 instances and the ALB.

## 1. Description

This template performs the following tasks:
- Creates a VPC with the specified CIDR bloc.
- Sets up two Availability Zones (AZs) with public and private subnets in each AZ.
- Deploys an Internet Gateway for internet access in public subnets.
- Configures NAT Gateways in each AZ for outbound internet access from private subnets.
- Creates public and private route tables with the routing rules for the traffic.
- Launches EC2 instances in private subnets with a simple web page displaying the instance's AZ.
- Sets up an Application Load Balancer to distribute traffic between the EC2 instances.
- Creates the necessary security groups for the EC2 instances and the ALB.

## 2. Parameters

The template accepts the following parameters:

| Parameter | Description | Type | Default |
|-----------|-------------|------|---------|
| `ProjectName` | The project name that is prefixed to resource names | String | WebApp |
| `VpcCIDR` | The IP range (CIDR notation) for the VPC | String | 10.0.0.0/16 |
| `PublicSubnet1CIDR` | The IP range (CIDR notation) for the public subnet in the first Availability Zone | String | 10.0.1.0/24 |
| `PrivateSubnet1CIDR` | The IP range (CIDR notation) for the private subnet in the first Availability Zone | String | 10.0.2.0/24 |
| `PublicSubnet2CIDR` | The IP range (CIDR notation) for the public subnet in the second Availability Zone | String | 10.0.3.0/24 |
| `PrivateSubnet2CIDR` | The IP range (CIDR notation) for the private subnet in the second Availability Zone | String | 10.0.4.0/24 |

## 3. Resources

The template creates the following resources:

- **VPC**: A Virtual Private Cloud with the specified CIDR block.
- **Subnets**: Public and private subnets in each of the two Availability Zones for high availability and fault tolerance.
- **Internet Gateway**: An Internet Gateway for internet access in public subnets.
- **NAT Gateways**: NAT Gateways with assigned Elastic IP in each AZ for outbound internet access from private subnets.
- **Public Route table**: Public route table to define the routing rules for the traffic in the public subnet.
- **Private Route table**: Private route table in each AZ to define the routing rules for the traffic in the private subnet.
- **EC2 Instances**: Two EC2 instances in private subnets in each AZ, each running a simple web server.
- **Application Load Balancer**: An ALB in the public subnet to distribute traffic between the EC2 instances.
- **Security Groups**: Security groups as firewall for the EC2 instances and the ALB.

All the resources are tagged with the project name to easily identify them in the AWS Management Console and for billing purposes.

## 4. Outputs

The template provides the following outputs:

| Output | Description |
|--------|-------------|
| `LoadBalancerDNSName` | The DNS name of the Application Load Balancer |

## 5. Usage

### 5.1 Deploying the Template

You can deploy this CloudFormation template using the AWS Management Console, AWS CLI, or AWS SDKs.

#### 5.1.1 AWS Management Console

1. Open the AWS Management Console.
2. Navigate to the CloudFormation service.
3. Click on "Create stack" and select "Choose an existing template".
4. Upload the template file or specify the S3 URL where the template is stored.
5. Provide the stack name and the required parameters and click "Next".
6. Follow the prompts to complete the stack creation.
7. Navigate through the different tabs of the stack to view the creation status, events, resources, outputs...

#### 5.1.2 AWS CLI

From the root of the project, you can run the following command to create the stack:

```sh
aws cloudformation create-stack --stack-name web-app-stack --template-body file://template/vpc-ec2-elb.yaml --parameters ParameterKey=ProjectName,ParameterValue=WebApp ParameterKey=VpcCIDR,ParameterValue=10.0.0.0/16 ParameterKey=PublicSubnet1CIDR,ParameterValue=10.0.1.0/24 ParameterKey=PrivateSubnet1CIDR,ParameterValue=10.0.2.0/24 ParameterKey=PublicSubnet2CIDR,ParameterValue=10.0.3.0/24 ParameterKey=PrivateSubnet2CIDR,ParameterValue=10.0.4.0/24
```

To check the status of thee stack, you can run the following command:

```sh
aws cloudformation describe-stacks --stack-name web-app-stack
```

To list the resources of thee stack, you can run the following command:

```sh
aws cloudformation list-stack-resources --stack-name web-app-stack
```

### 5.2 Accessing the Application

After the stack is created, you can access the web application using the DNS name of the Application Load Balancer, which is provided in the stack outputs. You can just open the URL with your browser. By clicking many times, you can see that the availability zone that is displayed is alternating.

You can also use the command line to access the website and hit the Load Balancer in a loop with the following:
```sh
while true; do curl http://DNSNameOfTheApplicationLoadBalancer; sleep 2; done
```

### 5.3 Deleting the stack

You can delete this CloudFormation stack including all the created resources using the AWS Management Console, AWS CLI, or AWS SDKs.

#### 5.3.1 AWS Management Console

1. Open the AWS Management Console.
2. Navigate to the CloudFormation service.
3. Click on "Stacks" and select your stack.
4. Click on "Delete" and confirm the deletion

#### 5.3.2 AWS CLI
You can run the following command to delete the stack:

```sh
aws cloudformation delete-stack --stack-name web-app-stack
```