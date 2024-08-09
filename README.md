---
title: developing on amazon ecs with cloudformation
author: haimtran
date: 07/07/2024
---

## Introduction

This sample project shows how to getting started with developing on Amazon ECS using CloudFormation. It demonstrates the following features:

- Create an Amazon ECS cluster
- Deploy a simple service
- Implement a standard CI/CD pipeline
- Implement a blue green CI/CD pipeline

## Project Structure

The sample consists of CloudFormation templates and a simple web application. Below is detailed project structure:

```go
|--book-app
   |--appspec.yaml
   |--Dockerfile
   |--index.html
   |--taskdef.json
|--templates
   |--1-network.yaml
   |--2-load-balancer.yaml
   |--3-ecs-cluster.yaml
   |--4-task-def-book.yaml
   |--5-service-book.yaml
   |--6-codepipeline.yaml
   |--22-load-balancer-blue-green.yaml
   |--23-s3-ecr.yaml
   |--25-service-book-blue-green.yaml
   |--26-codepipeline-blue-green.yaml
|--README.md
```

## Deploy A Simple Service

First, let's create a VPC by launching 1-network.yaml stack.

```bash
aws cloudformation create-stack \
 --stack-name cfn-network \
 --template-body file://1-network.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Second, let's create an application load balancer by launching 2-load-balancer.yaml stack.

```bash
aws cloudformation create-stack \
 --stack-name cfn-load-balancer \
 --template-body file://2-load-balancer.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Third, let's create a Amazon ECS cluster by launch 3-ecs-cluster.yaml.

```bash
aws cloudformation create-stack \
 --stack-name cfn-ecs-cluster \
 --template-body file://3-ecs-cluster.yaml \
 --capabilities CAPABILITY_NAMED_IAM

```

Fourth, create a task definition for book applciation by launching 4-task-def-book.yaml stack.

```bash
aws cloudformation create-stack \
 --stack-name cfn-task-def-book \
 --template-body file://4-task-def-book.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Finally, create the book service by launch the 5-book-service.yaml stack.

```bash
aws cloudformation create-stack \
 --stack-name cfn-service-book \
 --template-body file://5-service-book.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

## Deploy Standard Pipeline

First let's create a codecommit repository, an ecr repository and a S3 bucket for codepipeline artifacts by launching 23-codecommit-ecr.yaml.

```bash
aws cloudformation create-stack \
 --stack-name cfn-codecommit-ecr-stack \
 --template-body file://23-s3-ecr.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Second, let's create a CodePipeline consisting of CodeCommit, CodeBuild and CodeDeploy by lauching 6-codepipeline.yaml stack. The CodeDeploy will perform a standard Amazon ECS deployment which is rolling update.

```bash
aws cloudformation create-stack \
 --stack-name cfn-codepipeline \
 --template-body file://6-codepipeline.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

## Deploy Blue Green Pipeline

First, let's create another application load balancer by launching 22-load-balancer-blue-green.yaml stack.

```bash
aws cloudformation create-stack \
 --stack-name cfn-alb-blue-green \
 --template-body file://22-load-balancer-blue-green.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Second, let's create another book service by launching 25-service-book-blue-green.yaml.

```bash
aws cloudformation create-stack \
 --stack-name cfn-service-book-blue-green \
 --template-body file://25-service-book-blue-green.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```

Third, let's create a CodePipeline with Blue/Green ECS Deployment supported by CodeDeploy by launching 26-codepipeline-blue-green.yaml

```bash
aws cloudformation create-stack \
 --stack-name cfn-pipeline-blue-green \
 --template-body file://26-codepipeline-blue-green.yaml \
 --capabilities CAPABILITY_NAMED_IAM
```
