# Hands-on Lambda-01 : Lambda Function and API Gateway

The topics for this hands-on session will be AWS Lambda, function as a service (FaaS). During this Playground we will create two AWS S3 Buckets and using AWS Lambda to synchronize them. In addition, We will show the association between Lambda and API Gateway.

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- Create a S3 bucket.

- Create Lambda Function

- Trigger the Lambda Function with S3 Bucket

- Create a lambda function, that generates a random city and trigger it with API Gateway.

## Outline

- Part 1 - Prep - Creating AWS S3 Bucket

- Part 2 - Creating a Lambda Function and Setting a Trigger Event

- Part 3 - Create a Lambda Function with API Gateway

## Part 1 - Prep - Creating a S3 Bucket

STEP 1 : Prep - Creating S3 Bucket

- Go to S3 menu using AWS console

- Create a bucket of `kenan.source.lambda.bucket` with following properties,

```text
Bucket name                 : kenan.source.lambda.bucket
Region                      : N.Virginia
Block all public access     : Checked (KEEP BlOCKED)
Versioning                  : Disabled
Tagging                     : 0 Tags
Default encryption          : None
Object-level logging        : Disabled
```

PS: Please, do not forget to select "US East (N.Virginia)" as Region

- Create another bucket of `kenan.destination.lambda.bucket` with following properties,

```text
Bucket name                 : kenan.destination.lambda.bucket
Region                      : N.Virginia
Block all public access     : Checked (KEEP BlOCKED)
Versioning                  : Disabled
Tagging                     : 0 Tags
Default encryption          : None
Object-level logging        : Disabled
```

PS: Please, do not forget to select "US East (N.Virginia)" as Region
![Alt text](images/image.png)
![Alt text](images/image-1.png)
![Alt text](images/image-2.png)
![Alt text](images/image-3.png)

## Part 2 - Create a Lambda Function and Trigger Event

# STEP 1: Create IAM Role

Go to IAM page.

- Go to `Roles` on the left hand menu and click `create role`.

```text
Type of Trusted Entity      : AWS Service
Use Case                    : Lambda
Permissions                 : AmazonS3FullAccess 
Name:                       : kenan.IAMRole.S3FullAccess
```

![Alt text](images/image-4.png)
![Alt text](images/image-5.png)
![Alt text](images/image-6.png)
![Alt text](images/image-7.png)

# STEP 2: Create Lambda Function

- Go to Lambda Service on AWS Console

- Functions ----> Create Lambda function

```text
1. Select Author from scratch
   -Author from scratch
  Name: lambda_func_1
  Runtime: Python 3.9
  Change default execution role :
  Execution role >>>> Use an existing role ( 'Select kenan.IAMRole.S3FullAccess' )
  Role: Select kenan.IAMRole.S3FullAccess
  
```

![Alt text](images/image-8.png)
![Alt text](images/image-9.png)
![Alt text](images/image-10.png)

# STEP 3: Setting Trigger Event

- Go to Configuration sub-menu and click AddTrigger on Designer  

```bash
Add trigger
Trigger Configuration : S3

- Bucket              : kenan.source.lambda.bucket

- Event Type          : All object create events

- Recursive invocation         : checked
```

![Alt text](images/image-11.png)
![Alt text](images/image-12.png)

# STEP 4: Create Function Code

- Go to the Function Code sub-menu and paste >>> >>>> code seen below:

```python
import json
import boto3

# boto3 S3 initialization
s3_client = boto3.client("s3")


def lambda_handler(event, context):
   destination_bucket_name = 'kenan.destination.lambda.bucket'

   # event contains all information about uploaded object
   print("Event :", event)

   # Bucket Name where file was uploaded
   source_bucket_name = event['Records'][0]['s3']['bucket']['name']

   # Filename of object (with path)
   file_key_name = event['Records'][0]['s3']['object']['key']

   # Copy Source Object
   copy_source_object = {'Bucket': source_bucket_name, 'Key': file_key_name}

   # S3 copy object operation
   s3_client.copy_object(CopySource=copy_source_object, Bucket=destination_bucket_name, Key=file_key_name)

   return {
       'statusCode': 200,
       'body': json.dumps('Hello from S3 events Lambda!')
   }
```

- Click "DEPLOY" button
  
![Alt text](images/image-13.png)

# STEP 5: Testing S3 Bucket Synchronization

- Go to S3 Bucket Service

- Select S3 bucket named 'kenan.source.lambda.bucket'

- Upload any files to source bucket

- Go to the S3 bucket named 'kenan.destination.lambda.bucket' and show the uploaded files to the source bucket.
  
![Alt text](images/image-14.png)

- You can show some other functions if have enough time (Optional)

- Example 2: Create function
Create function
Author from scratch
 Function name: lambda_func_2
 Runtime: Python 3.7
  
![Alt text](images/image-15.png)

```python
from __future__ import print_function
from random import randint
print('Loading function')
def lambda_handler(event, context):
    myNumber = randint(1,3999)
    number = myNumber
    roman = ['M', 'CM', 'D', 'CD', 'C', 'XC', 'L', 'XL', 'X', 'IX', 'V', 'IV', 'I']
    sayi = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
    romanvalue = ""
    for i, d in enumerate(sayi):
        while (myNumber >= d):
            myNumber -= d
            romanvalue += roman[i]
    return f'Roman Representation of the {number} is {romanvalue}'

```

Deploy >> test

For testing:

{

}

>> save
>> test

![Alt text](images/image-16.png)
![Alt text](images/image-17.png)

- Example 3:
Create function
Author from scratch
 Function name: lambda_func_3
 Runtime: Python 3.7

```python

def lambda_handler(event, context):
 roman = ['M', 'CM', 'D', 'CD', 'C', 'XC', 'L', 'XL', 'X', 'IX', 'V', 'IV', 'I']
 sayi = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
 romanvalue = ""
 for i, d in enumerate(sayi):
   while (event['num'] >= d):
      event['num'] -= d
      romanvalue += roman[i]
 return romanvalue
```

Deploy
test

For testing:

{
  "num": 1257
}

![Alt text](images/image-18.png)
![Alt text](images/image-19.png)
![Alt text](images/image-20.png)

## Part 3 - Create a Lambda Function with API Gateway

# STEP 1: Create Lambda Function

- Go to Lambda Service on AWS Console

- Functions ----> Create Lambda function

```text
1. Select Author from scratch
  Name: lambda_APIGateway_func
  Runtime: Python 3.7
  Change default execution role
  Execution role: Create a new role with basic Lambda permissions
  Click 'Create function'
```

![Alt text](images/image-21.png)

- Configuration of Function Code
- In the sub-menu of configuration go to the "Function code section" and paste code seen below

```python
from __future__ import print_function
from random import randint

print('Loading function')

def lambda_handler(event, context):
    myNumber = randint(0,8)
    mylist = ['Berlin', 'London', 'Athens', 'New York', 'Istanbul', 'Ankara', 'Brussels','Paris','CapeTown']
    return mylist[myNumber]
```

- Click "DEPLOY" button
  
![Alt text](images/image-22.png)

# STEP 2: Testing your function - Create test event

Click 'Test' button and opening page Configure test events

```text
Select: Create new test event
Event name: test-5
Event sharing settings: Select 'Private'
Event template: hello-world
Input test event as;
{
  
}

Click 'Create'
Click 'Test'
```

![Alt text](images/image-23.png)

You will see the message Execution result: succeeded(logs) and a random city in a box with a dotted line.

![Alt text](images/image-24.png)

# STEP 3 : Create New 'API'

- Go to API Gateway on AWS Console

- Click "Create API"

- Select REST API ----> Build
  
![Alt text](images/image-25.png)

```text
Choose the protocol : REST
Create new API      : New API
Settings            : cx

  - Name: MyFirstAPI
  - Description: my test first api
  - Endpoint Type: Regional
  - Click 'Create API'
```

![Alt text](images/image-26.png)

# STEP 4 : Exposing Lambda via API Gateway

1. Add a New Child Resource

- APIs > FirstAPI > Resources > /

- Click on Actions > 'Create Resource'

   New Child Resource
  - Configure as proxy resource: Leave blank
  - Resource Name: city
  - Resource Path: /city (it has automatically appear)
  - Enable API Gateway CORS: Yes
  - Click 'Create Resource' button
![Alt text](images/image-27.png)
![Alt text](images/image-28.png)

2. Add a GET method to resource /city

- Actions > Create Method

- Under the resource a drop down will appear select GET method and click the 'tick'.
     /city
     OPTIONS
![Alt text](images/image-29.png)

3. city/GET >>> Method Execution

```text
  - Integration type: Lambda Function
  - Use Lambda Proxy integration: Leave blank
  - Lambda Region: us-east-1
  - Lambda Function: lambda_APIGateway_func
  - Click 'Save'
  - Confirm the dialog 'Add Permission to Lambda Function', Click 'OK'
```

![Alt text](images/image-30.png)

# STEP 5: Testing Lambda via API Gateway

- Click GET method under /city

- Click TEST link in the box labeled 'Client At the bottom of the new view Click 'Test' button

- Under Response Body you should see a random city. Click the blue button labelled 'Test' again at the bottom of the screen and you will see a new city appear.

- Test completed successfully

![Alt text](images/image-31.png)
![Alt text](images/image-32.png)

# STEP 6 : Deploy API

- Click Resources select /city

- Select Actions >>>>> Deploy API

![Alt text](images/image-33.png)

```text
Deployment stage: [New Stage]
Stage Name: dev
```

![Alt text](images/image-34.png)

- it can be seen invoke URL on top like;
"<https://b8pize8i6e.execute-api.us-east-1.amazonaws.com/dev>" and note it down somewhere.

- Entering the Invoke URL into the web browser adding "/city"and show the generated city with refreshing the page in the web browser.

![Alt text](images/image-35.png)

- **NOTE**: Don't forget to do these
Delete:
Lambda functions
APIs
S3 bucket
