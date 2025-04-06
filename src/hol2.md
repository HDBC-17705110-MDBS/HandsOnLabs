# HOL 02 - Deploying a hybrid infrastructure for researchers in AWS

## Introduction

Jupyter Notebooks have become an essential tool for analyzing data and disseminating findings in data science. This hands-on lab guides you through setting up a public Jupyter Notebook server on AWS for your research team which is going to save images to an S3 bucket monitored by a Lambda that processes them and saves the final result on another bucket. Furthermore, we will deploy a private FileGator file server using Docker to share sensitive data between your team which will be accessible only through a VPN connection. 

## Prerequisites

- AWS Academy account. (Check out [this guide](./guide1.md) for help).
- Access to the AWS Management Console.
- Knowing how to setup the **AWS CLI Credentials**. (Check out [this guide](./guide2.md) for help).
- Basic knowledge of AWS services, including **EC2, S3, Lambda, and VPC**. (Check out the different sessions in the course for help, a summary of each session can be found on its page on this website, check [the index](https://hdbc-17705110-mdbs.github.io/HandsOnLabs/index.html) on the home page).

## Architecture Diagram

The figure below provides a visual representation of the proposed infrastructure. It depicts a Virtual Private Cloud (VPC) on AWS, segmented into two subnets: *Public and Private*. The Public Subnet hosts an EC2 instance named **lab-public-ec2**, which is Internet-accessible. The Private Subnet accommodates an EC2 instance named **lab-private-ec2**, accessible via VPN. The VPC also contains a Client VPN endpoint, which allows us to simulate we are on the public subnet and makes it possible to access resources on the private subnet.

The public EC2 instance will be interacting with an S3 bucket using **boto3 library for python** which is going **to trigger a Lambda**. The idea is to run a piece of Python code on a Jupyter Notebook that generates an example image and uploads it to the input S3 bucket, then a lambda is triggered and saves that image to the output bucket with a different name. 

The private EC2 instance will just be serving the file server and will need to temporarily have internet access to install the required software by using a NAT Gateway. The VPN endpoint will be used both to connect to the private EC2 instance by SSH and configure the file server, and once the file server is up and running, to access it through the browser on our local machine.

![Architecture Diagram](./figs/hol02/architecture.png)

```admonish danger title="Important"
This lab is going to be divided into two tasks that should be implemented **in order**. Everything related to the Client VPN endpoint is going to be on the second task, I suggest you **only go for Task 2 once Task 1 is complete** since the Client VPN endpoint is a **substantially expensive** service. By leaving Task 2 for the end we can make it easier for your budget to not run out before you have completed the lab.

Moreover, **Task 1 is going to be 80% of this assignment grade** while Task 2 will be the remaining 20%. I prefer you to fully complete Task 1 before attempting Task 2. Once Task 1 is done, **keep an eye on you budget** (an email is going to automatically be sent to you once 50%, 90% and 95% of your budget is exceeded). If you see you are soon going to run out of budget just stop the lab and email me at `ferran.aran@udl.cat`, I'll set up a fresh account for you. **Running out of budget means losing everything on your account!!**
```

## Task 1

This task's goal is to set up the infrastructure for the public Jupyter Notebook server, the S3 buckets and the Lambda function. Below is the order in which I would recommend to proceed:

1. Create the VPC and the public subnet.
2. Add the **Internet Gateway** to the VPC and **attach it to the public subnet**.
3. Create a **Route Table** for the public subnet and associate it **with the Internet Gateway**.
4. Create an EC2 instance in the public subnet and configure the security group to **allow SSH (port 22) and HTTP access on port 8888**.
5. Connect to the EC2 instance **through SSH**, setup python (whichever version you want) and install the required libraries (`boto3`, `jupyter`, `pillow`).
6. Create the **S3 buckets** and the **Lambda function**. Configure the Lambda function to trigger when a new image is uploaded to the input bucket and the code (**which I will provide below**) to make it process the image and upload it to the output bucket.
7. Configure **AWS CLI Credentials on the EC2 instance**, launch the Jupyter Notebook server and run the code (**which I will provide below**) to upload the image to the input bucket.
8. **Check** if the Lambda function is triggered and the image is processed and uploaded to the output bucket.

### Services

Here are the services you will need to create in the AWS Management Console:

```admonish info
The list below **does not** contain all the parameters you need to fill in, just the ones that could vary from what we saw in class. You'll need to find out the rest of the parameters by yourself (just look at the slides, everything is there 😉)

For this task I would recommend to visit [Session 3](./session3.md) and [Session 4](./session4.md) of the course.
```

- VPC: 
  - Name: lab-vpc
  - CIDR: 10.0.0.0/16
    
- Subnets: 
  - Name: lab-public-subnet
    - CIDR: 10.0.1.0/24

- Internet Gateway:
  - Name: lab-igw

- Route Table:
  - Name: lab-public-to-internet
  - Association: lab-public-subnet
  - Routes: 0.0.0.0/0 -> lab-igw 

- EC2 Instances:
  - Name: lab-public-ec2
    - Subnet: lab-public-subnet
    - AMI: Amazon Linux 1 # TODO Check if it is 2 or 1 by default

- S3 Buckets:
  - Name: lab-input-bucket-[YOUR-NAME]
  - Name: lab-output-bucket-[YOUR-NAME]
  
- Lambda Function:
  - Name: lab-lambda-function
  - Runtime: Python 3.13
  
### Additional resources

Below are the code snippets you will need for the first task, **remember to replace the bucket names with your own**. 

Here is the python code to use for the Lambda function:

```python
import boto3
import json
import os
import urllib.parse

s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Extract bucket and image info from the S3 event
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])
    original_name = os.path.splitext(os.path.basename(key))[0]
    download_path = f'/tmp/{os.path.basename(key)}'
    s3.download_file(bucket, key, download_path)

    # Upload it to the "output" bucket with a different name
    result_image_name = f"{original_name}-processed.png"
    result_bucket = 'medical-images-processed-[YOUR-NAME]' # Replace with your bucket name
    s3.upload_file(download_path, result_bucket, result_image_name)

    return {
        'statusCode': 200,
        'body': json.dumps(f"Processed {key}")
    }
```

And here is the python code to use for the Jupyter Notebook:

```python
from PIL import Image, ImageDraw
import boto3
import io

# Create an example image
image = Image.new('RGB', (200, 100), color='white')
draw = ImageDraw.Draw(image)
draw.text((50, 40), "Hello!", fill='black')
buffer = io.BytesIO()
image.save(buffer, format='PNG')
buffer.seek(0)

# Upload to S3
bucket_name = 'lab-output-bucket-[YOUR-NAME]'   # <-- Replace with your bucket name

s3 = boto3.client('s3')
object_key = 'lab-image.png'
s3.upload_fileobj(buffer, bucket_name, object_key)

print(f"Image uploaded to s3://{bucket_name}/{object_key}")
```

## Task 2

This task's goal is to set up the infrastructure for the private FileGator server. Below is the order in which I would recommend to proceed:

1. Create the private subnet.
2. Create an EC2 instance on the private subnet and configure the security group to allow SSH (port 22) and HTTP access on port 8080.
3. Temporarily give internet access to the private EC2 instance by adding a NAT Gateway and modifying the route table of the private subnet. 
4. Generate server and client certificates and import them to AWS.
5. Add the Client VPN endpoint to the VPC and associate it with the public subnet.
6. Configure the Route Table and the Authorization Rules for the Client VPN endpoint.
7. Download the VPN configuration file ending with `.ovpn` and edit it to include the client certificate and key.
8. Download and install the [AWS Client VPN software](https://aws.amazon.com/vpn/client-vpn-download/) on your local machine and import the configuration file.
9. Connect to the VPN and SSH into the private EC2 instance.
10. Install Docker and run the FileGator server on the private EC2 instance.
11. Remove the NAT Gateway and modify the route table of the private subnet to remove the internet access.
12. Make sure the FileGator server is accessible only when you are connected to the VPN.


### Services 
Here are the services you will need to create in the AWS Management Console:

```admonish info
Once again, the list below **does not** contain all the parameters you need to fill in, just the ones that could vary from what we saw in class.

For this task I would recommend to visit [Sessions 6, 7 and 8](./session6-7-8.md) of the course.
```

- Subnets: 
  - Name: lab-private-subnet
    - CIDR: 10.0.2.0/24

- EC2 Instances:
  - Name: lab-private-ec2
  - Subnet: lab-private-subnet
  - AMI: Ubuntu
    
- NAT Gateway (*will have to be deleted once the EC2 instance is configured*):
  - Name: lab-nat-gateway
  - Elastic IP: lab-nat-eip

- Route Table (*will have to be deleted once the EC2 instance is configured*):
  - Name: (no name) it is the default route table of the VPC
  - Routes: 0.0.0.0/0 -> lab-nat-gateway 

- Client VPN Endpoint:
  - Name: lab-client-vpn-endpoint
  - Client CIDR: 10.83.0.0/16
  
## Deliverables

- A PDF report to be submitted on the Moodle ([here](https://campusvirtual.urv.cat/mod/assign/view.php?id=4051057)). The report should just contain screenshots that demonstrate each of the task steps defined above. Screenshots can be accompanied by a short description of what is being shown and any other relevant information.

## Minimum requirements

The minimum requirements to pass (get 5/10 points) the assignment are:

- The public EC2 instance is up and running, with the Jupyter Notebook server running. 

- The input S3 bucket is created and running the provided python code on the Jupyter Notebook server uploads an image to the input bucket. 