# AWS CloudFormation + EC2 + RDS and Docker App

Devteds [Episode #7](https://devteds.com/episodes/7-create-aws-cloudformation-stack-for-ec2-rds-and-deploy-docker-app)

Create an application stack that consist of an EC2 instance and RDS Instance for MySQL, using AWS CloudFormation. And then deploy an dockerized / containerized API application to run on EC2 instance.

[Episode video link](https://youtu.be/8J0g_xWUzV0)

[![Episode Video Link](https://i.ytimg.com/vi/8J0g_xWUzV0/hqdefault.jpg)](https://youtu.be/8J0g_xWUzV0)

Visit https://devteds.com to watch all the episodes. If you're looking to learn [Docker with ECS](https://github.com/devteds/e9-cloudformation-docker-ecs) checkout [episode #9](https://devteds.com/episodes/9-docker-on-amazon-ecs-using-cloudformation)

## Setup AWS & CLI

- Login to AWS Management Console and Generate a new KeyPair. Save the downloaded key
- Configure AWS Command Line Interface (AWS CLI) with a new AWS Access Key and a secret generated on AWS
- Update the stack.yml if you're using this template for your application
- Use AWS CLI to run create-stack command that'll create all the resources defined in the stack.yml
- Update the docker-compose app.yml if you're using this for your application
- Using docker compose run database migration command, run service in background and run db-seed to populate test data

## Commands

```
mkdir ~/projs
git clone https://github.com/devteds/e7-cloudformation-docker.git deploy-aws
cd deploy-aws

# Create new KeyPair on AWS CLI and name it aws-key1 and,
cp ~/Downloads/aws-key1.pem ./
chmod 400 aws-key1.pem

# On AWS CLI, Create a new user with programatic access which will generate a new Access Key & Secret. With that,
aws configure --profile demo
# Make sure to use the region name (us-west-2 or another) as the default for this profile 'demo'

aws cloudformation create-stack --profile demo --stack-name blog-stage --template-body file://$PWD/stack.yml

# After the stack creation is successful, Get the IP address or DNS of the AppNode EC2 instance
ssh -i aws-key1.pem ubuntu@<IP ADDRESS OR DNS OF THE EC2 INSTANCE>

# Point your local to EC2 instance,
export DOCKER_HOST=tcp://35.160.122.95:2375
docker ps -a

docker-compose -f app.yml run --rm app rails db:migrate
docker-compose -f app.yml up -d
docker-compose -f app.yml ps
docker-compose -f app.yml run --rm app rails db:seed

# Make sure to delete the stack and all resources when you're done
aws cloudformation delete-stack --stack-name blog-stage --profile demo
```

## More useful commands

```
aws cloudformation describe-stack-resources --stack-name blog-stage --profile demo
aws cloudformation describe-stack-events --stack-name blog-stage --profile demo

# For more help
aws cloudformation help
aws cloudformation create-stack help
aws cloudformation delete-stack help
aws cloudformation delete-stack help
aws cloudformation describe-stack-resources help
```


Stack is defined using YAML template that contains 4 resources to be created on AWS,

1. EC2 Instance to run the application on
2. EC2 Security Group that defines the possible inbound ports on the EC2 instance
3. RDS Instance for MySQL database
4. RDS Security Group that defines the possible sources the db can be reached from

https://devteds.com/episodes/7-create...

Following are the steps to walk through,

1. Create a KeyPair on AWS account to be used for SSH access to EC2
2. Generate API keys and configure the AWS CLI on local machine
3. Create a simple stack using CloudFormation and this stack will consists of an EC2 instance and necessary security group for EC2 instance
4. Update the stack to install and configure docker on the EC2 instance
5. Update the stack to add RDS instance and necessary security group to the stack
6. Create Docker compose file to define the application to be deployed on EC2 instance with it’s database on RDS instance
7. Deploy the application on the EC2 instance (docker host) remotely from local