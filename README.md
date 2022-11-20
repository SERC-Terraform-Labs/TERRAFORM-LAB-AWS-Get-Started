# Terraform Lab - AWS Get Started

Introduction to Terraform basics by building, changing, and destroying infrastructure on Amazon Web Services (AWS). You will use `terraform` to provision an EC2 instance (virtual machine) on AWS.

In this lab you will:

- Create AWS infrastructure by using `terraform init`, `terraform plan`, and `terraform apply`.
- Change AWS infrastructure by using `terraform plan` and `terraform apply`.
- Destroy AWS infrastructure by using `terraform destroy`.
- Use input variables to customize your infrastructure.
- Use output values to display important information about your infrastructure.
- (Use Terraform Cloud to store state and run Terraform in the cloud.)

This lab is based on the tutorial https://developer.hashicorp.com/terraform/tutorials/aws-get-started.

Add changes to version control as you go through the steps in the tutorial. This will make it easier to see which configuration settings have changed.

### Lab Setup and Prerequisites

The lab includes template settings for a [Codespace](https://docs.github.com/en/codespaces/getting-started/quickstart) preconfigured with the necessary software to run the lab. This includes an install of [LocalStack](https://github.com/localstack/localstack) to mock AWS services locally so that an AWS account is not needed.

If you do not want to use a Codespace, you will need to install and run the following software locally:

- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- [Terraform CLI](https://learn.hashicorp.com/tutorials/terraform/install-cli)
- [LocalStack](https://github.com/localstack/localstack) (optional)

If you want to deploy infrastructure to AWS during the lab, you will need an AWS account and you will need to configure the AWS CLI with your credentials.

- [AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

> **Warning**: Some of the AWS resources provisioned in this tutorial may not be covered by the [AWS free tier](https://aws.amazon.com/free). There may be some small charges incurred running the lab on AWS. If you leave resources provisioned and don't `terraform destroy` at the end, there definitely will be charges. Any charges incurred are solely your responsibility.

<details><summary>Launch Codespace</summary>

You need a GitHub account to use Codespaces and you need to be logged in.

If you have accepted a GitHub Classroom assignment, you can launch a Codespace by clicking the green "Code" button and selecting "Open with Codespaces". This will create a Codespace preconfigured with the necessary software to run the lab. Further instructions are available in the [GitHub Docs](https://docs.github.com/en/codespaces/developing-in-codespaces/creating-a-codespace-for-a-repository#creating-a-codespace-for-a-repository).

If you are following directly from the lab template repository, you can launch a Codespace by clicking the green "Use this template" button and selecting "Open with Codespaces". This will create a Codespace preconfigured with the necessary software to run the lab. Further instructions are available in the [GitHub Docs](https://docs.github.com/en/codespaces/developing-in-codespaces/creating-a-codespace-for-a-repository#creating-a-codespace-for-a-repository).

</details>

<details><summary>AWS CLI Credentials</summary>

### Create Access Keys

1. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
1. In the navigation pane, choose Users.
1. Choose your IAM user name (not the check box).
1. Choose the Security credentials tab and then choose Create access key.
1. To view the new access key pair, choose Show. You will not have access to the secret access key again after this dialog box closes. Your credentials will look something like this:

```bash
AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Configure the AWS CLI

You will need to add your AWS Access Keys to the AWS CLI client. Configure the AWS CLI from the terminal. Follow the prompts to input your AWS Access Key ID and Secret Access Key.

```bash
aws configure
```

The configuration process stores your credentials in a file at `~/.aws/credentials` within the Codespace workspace.

</details>

<details><summary>Mocking AWS - LocalStack</summary>

Instead of applying changes to the real AWS, we can create a fake, local version of AWS and test our configuration against this fake version. This will mean no real resources are provisioned in AWS. To do this, we will use an emulator called [LocalStack](https://github.com/localstack/localstack).

### Starting LocalStack

LocalStack is already installed in the Codespace. To start the LocalStack container, run the following command in the terminal:

```bash
sudo localstack start -d
```

> **Note**: If you have problems running the `localstack` command, you can start LocalStack directly using Docker:
> 
> ```bash
> docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack
> ```
> 

### Configure Terraform Resource Provider

In `main.tf`, replace the `provider "aws"` block

```terraform
provider "aws" {
  region  = "us-west-2"
}
```

with the following:

```terraform
provider "aws" {
  region                      = "us-west-2"
  access_key                  = "mock_access_key"
  secret_key                  = "mock_secret_key"
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true
  s3_use_path_style           = true

  endpoints {
    apigateway     = "http://localhost:4566"
    cloudformation = "http://localhost:4566"
    cloudwatch     = "http://localhost:4566"
    dynamodb       = "http://localhost:4566"
    es             = "http://localhost:4566"
    firehose       = "http://localhost:4566"
    iam            = "http://localhost:4566"
    kinesis        = "http://localhost:4566"
    lambda         = "http://localhost:4566"
    route53        = "http://localhost:4566"
    redshift       = "http://localhost:4566"
    s3             = "http://localhost:4566"
    secretsmanager = "http://localhost:4566"
    ses            = "http://localhost:4566"
    sns            = "http://localhost:4566"
    sqs            = "http://localhost:4566"
    ssm            = "http://localhost:4566"
    stepfunctions  = "http://localhost:4566"
    sts            = "http://localhost:4566"
    ec2            = "http://localhost:4566"
  }
}
```

Log-in to AWS again, using the fake credentials:

```
access_key: mock_access_key
secret_key: mock_secret_key
```

</details>

## 1. Build Infrastructure

Start with the 'Build Infrastructure' section of the tutorial: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build
