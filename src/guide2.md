# Guide 2 - Setting up AWS for each subsequent session

Once you have followed the [first guide](./guide1.md) to set up your AWS account, use this guide to set up your AWS environment for each session. That is for each time you want to work on your project and you start your AWS Learner Lab.

## Prerequisites

- AWS Academy account created
- An SSH key pair on your local machine
- An EC2 instance created with your key pair configured

## Introduction

Use this guide each time you want to work on your project and you need to start your AWS Learner Lab. If you successfully follow this guide you will end up with:

- A terminal on your local machine connected to the EC2 instance
- An EC2 instance running with AWS credentials configured and updated
- AWS credentials configured and updated on your local machine

## Step 1: Log in to your AWS account

To log in to your AWS account, visit the [AWS Academy](https://awsacademy.instructure.com/) website, click on `Student Login` and log in with your credentials.

```admonish info
Your browser may have kept your session open. If you end up in the AWS Academy dashboard you are good to go and can skip to [Step 2](#step-2-accessing-the-aws-dashboard).
```

<p align="center">
    <img src="./figs/guide02/login0.png">
</p>

It may ask for a verification code sent to your email. If so, check your email and enter the code. The email will look like this:

![AWS Academy verification code](./figs/guide02/login1.png)

You should now see the AWS Academy dashboard.

## Step 2: Accessing the AWS Dashboard

Exactly as we did on Guide 1's [Step 2](./guide1.md#step-2-accessing-the-aws-dashboard), we now have to access the AWS Dashboard. Below are the steps to do so:

The first thing you have to do is to click on the `Courses` tab on the left side of the screen. You will see a list of courses available to you. Click on the course `AWS Academy Learner Lab` as shown in the screenshot below:

<p align="center">
    <img src="./figs/guide01/login2.png">
</p>

Now we have to look for the `Modules` tab on the left side of the screen. Click on it and you will see a list of modules available to you. Click on the module `AWS Academy Learner Lab` as shown below:

<p align="center">
    <img src="./figs/guide01/login3.png", width="70%">
</p>


You need to now click on the `Start Lab` button.

![AWS Academy terms and conditions](./figs/guide01/login5.png)

Now give it a couple minutes to load. You'll see this animation while the lab is being prepared for you:

![AWS Academy terms and conditions](./figs/guide01/login6.png)

Finally, when the lab is ready, you'll see that the dot next to `AWS` is green. You'll also se a timer counting down from 4 hours. This is the time you have to work on the lab. To the left of the timer you'll see how's your budget going. You have a budget of $50 to spend on AWS services. Click on `AWS` to access the AWS Dashboard as shown below.

```admonish warning
If you exceed the $50 budget, you will no longer have acces to the AWS Dashboard and **will loose your work**. Make sure to keep an eye on the budget and if you see that you're getting close to the limit, let me know so I can create a new lab for you.
```

![AWS Academy terms and conditions](./figs/guide01/login7.png)

Great! You have now accessed the AWS Dashboard. You will see a screen similar to the one below:

![AWS Dashboard](./figs/guide01/login8.png)

## Step 3: Connect to your EC2 instance

Exactly as we did on Guide 1's [Step 5](./guide1.md#step-5-connecting-to-the-ec2-instance) we're now going to go to the EC2 service and look for the machine we want to connect to. As you may remember, we're going to use the search bar to look for the EC2 service. Click on the search bar and type `EC2`. Click on the first result that appears.

![AWS Dashboard](./figs/guide01/searchbar.png)

```admonish danger title="Important"
When the lab session ends **bacause the 4 hours have passed**, all your EC2 instances will be stopped (similarly to how one shuts down a computer). The next time you start a lab session, all your EC2 instances will automatically start again.

**If you instead have stopped the lab by clicking on the `Stop Lab` button**, you will have to manually start the EC2 instances you want to work with. To do so, you'll have to click on the instance you want to start and then on `Instance State` and finally on `Start instance` as shown below:

![AWS Dashboard](./figs/guide02/start1.png)

![AWS Dashboard](./figs/guide02/start2.png)

As said before, if the lab session ended naturally and not because you clicked on the `Stop Lab` button, the instances will have started automatically and this steps are not necessary.
```

Now go to the instances tab on the left, select the instance you want to connect to and copy the `Public IPv4 address` or `Public IPv4 DNS` (choose whichever you prefer as they both work).

![AWS Dashboard](./figs/guide01/ec2-3.png)

```admonish note
We have to repeat this step each time we start a new lab session because the IP address (and DNS) of EC2 instance changes each time they are restarted. And they are restarted each time we start a new lab session.
```

Once the IP address is copied, open a terminal on your local machine and connect to the EC2 instance using the following command:

```bash
ssh -i .ssh/aws-keypair ec2-user@<public-ip>
```
Both the `Public IPv4 address` and the `Public IPv4 DNS` can be used as the `<public-ip>` in the `ssh` command. Copy whichever you prefer and paste it in the `ssh` command. 

The command should look something like this if using the IP address:

```bash
ssh -i .ssh/aws-keypair ec2-user@98.84.68.70
```
or like this if using the DNS:

```bash
ssh -i .ssh/aws-keypair ec2-user@ec2-98-84-68-70.compute-1.amazonaws.com
```

You should now be connected to the remote machine. You will see a prompt similar to the one below:

```
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sun Mar  2 09:50:33 2025 from 79.156.58.43
[ec2-user@ip-172-31-86-82 ~]$
```

Fantastic! We are now connected to the remote machine. It is important to understand that from now on, the commands we type will be executed on the remote machine, not on our local machine.

## Step 4: Configure AWS credentials on the EC2 machine

From now on we are going to work on steps that where not covered on [Guide 1](./guide1.md). We are going to configure AWS credentials on the remote machine. This is important because we are going to use the AWS CLI to interact with AWS services.

```admonish info
The AWS CLI is a tool that allows you to interact with AWS services from the command line. It is a powerful tool that can be used to automate tasks and manage your AWS resources. For example, on [Session 4](./session4.md) we are going to use the AWS CLI to interact with the S3 service. You can find more information about the AWS CLI [here](https://aws.amazon.com/cli/).
```

When on an EC2 machine, AWS CLI comes already installed. You can check if it is installed by running the following command:

```
[ec2-user@ip-172-31-86-82 ~]$ aws

usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:

  aws help
  aws <command> help
  aws <command> <subcommand> help

aws: error: the following arguments are required: command

[ec2-user@ip-172-31-86-82 ~]$
```

This is the base command which by itself doesn't do anything. What we are going to do now is to configure the AWS CLI with our credentials so we can later run commands that can access other AWS resources like S3.

To configure the AWS CLI, run the following command:

```
[ec2-user@ip-172-31-86-82 ~]$ aws configure
AWS Access Key ID [None]:
```

You'll see we are being asked for the `AWS Access Key ID`. To get this key we have to go visit the AWS Academy website (not to be confused with the AWS Dashboard). Specifically we'll visit the page where we started the lab, in case you have closed it, below are the steps to get there:

![AWS Dashboard](./figs/guide02/login2.png)

![AWS Dashboard](./figs/guide02/login3.png)

We should now see the page where we launched the lab as shown below:

![AWS Dashboard](./figs/guide02/credentials0.png)

Now click on `AWS Details` and then on `Show` to see the `Access Key ID` and the `Secret Access Key`.

![AWS Dashboard](./figs/guide02/credentials1.png)

Once the credentials are shown we'll first need to copy the value of `Access Key ID`, so in the example below we would copy `ASIA2CKYVHJAGNNECYQM`.

<p align="center">
    <img src="./figs/guide02/credentials2.png">
</p>


Now go back to the terminal where we are configuring the AWS CLI and paste the `Access Key ID` and press `Enter`.

```
[ec2-user@ip-172-31-86-82 ~]$ aws configure
AWS Access Key ID [None]: ASIA2CKYVHJAGNNECYQM
AWS Secret Access Key [None]:
```

Now we are asked for the `AWS Secret Access Key`. Go back to the AWS Academy website and copy the value of `Secret Access Key`, now we could be copying the long text starting with `IQoJb3JpZ2luX2...`.

<p align="center">
    <img src="./figs/guide02/credentials3.png">
</p>

Paste the `Secret Access Key` in the terminal and press `Enter`. You will be asked for the `Default region name` and the `Default output format`. You can leave these empty by pressing `Enter` for both.

```
[ec2-user@ip-172-31-86-82 ~]$ aws configure
AWS Access Key ID [None]: ASIA2CKYVHJAGNNECYQM
AWS Secret Access Key [None]: IQoJb3JpZ2luX2...
Default region name [None]:
Default output format [None]:
```
To test if the configuration was successful, run `aws sts get-caller-identity` and you should see something like this:

```
[ec2-user@ip-172-31-86-82 ~]$ aws sts get-caller-identity
{
    "UserId": "AROA2CKYVHJALK46ZMHVM:user3869188=Ferran_Aran_Test",
    "Account": "692212546112",
    "Arn": "arn:aws:sts::692212546112:assumed-role/voclabs/user3869188=Ferran_Aran_Test"
}
[ec2-user@ip-172-31-86-82 ~]$
```

Great! You have now configured the AWS CLI on the remote machine. You can now run commands to interact with AWS services.

## Step 5: Configure AWS credentials on your local machine

The first thing is to have the AWS CLI installed on your local machine. Follow the steps on the [AWS CLI installation guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) to install the AWS CLI on your local machine.

To check if the AWS CLI is installed, run `aws --version` and you should see something like this:

```bash
aws --version
aws-cli/2.24.15 Python/3.12.9 Windows/10 exe/AMD64
```

Now we are going to configure the AWS CLI on your local machine. Run `aws configure` and you will be asked for the same information as on the remote machine. Follow the same steps as before to get the `Access Key ID` and the `Secret Access Key` and paste them when asked.

```bash
aws configure
AWS Access Key ID [None]: ASIA2CKYVHJAGNNECYQM
AWS Secret Access Key [None]: IQoJb3JpZ2luX2...
Default region name [None]:
Default output format [None]:
```

To test if the configuration was successful, run `aws sts get-caller-identity` and you should see something like this:

```bash
PS C:\Users\fnao> aws sts get-caller-identity
{
    "UserId": "AROA2CKYVHJALK46ZMHVM:user3869188=Ferran_Aran_Test",
    "Account": "692212546112",
    "Arn": "arn:aws:sts::692212546112:assumed-role/voclabs/user3869188=Ferran_Aran_Test"
}

PS C:\Users\fnao>
```

# TODO: Change pasting the credentials to writing .aws/credentials instead