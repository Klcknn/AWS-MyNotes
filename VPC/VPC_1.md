# Hands-on VPC-01 : Configuring of VPC

Purpose of the this hands-on training is to create VPC and configure VPC with components.

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- learn how to create VPC,

- learn how to create subnet,

- learn how to set route tables as public and private,

## Outline

- Creating VPC, Subnet and Subnet associations

## Creating VPC, Subnet and Subnet associations

### STEP 1: Create VPC

- First, go to the VPC and select Your VPC section from the left-hand menu, click create VPC.

- choose VPC Only
  ![Alt text](images/image_.png)
  ![Alt text](images/image.png)
- create a vpc named 'myVPC' with 172.16.0.0/16 CIDR

```text
    - IPv4 CIDR manual input
    - no ipv6 CIDR block
    - tenancy: default
```

- click create
  ![Alt text](images/image-1.png)

- explore the vpc details
  ![Alt text](images/image-2.png)
- enable DNS hostnames for the vpc 'myVPC'

  - Select 'myVPC' on VPC console ----> Actions ----> Edit VPc Settings ----> DNS settings, Enable DNS hostnames
  - Click enable flag
  - Click save
  ![Alt text](images/image-3.png)
  ![Alt text](images/image-4.png)

### STEP 2: Create an internet gateway named 'myIGW'

- Go to the Internet Gateways from left hand menu
  ![Alt text](images/image-5.png)
  ![Alt text](images/image-6.png)

- Click Create Internet Gateway
  - Name Tag "myVPC-IGW"
  - Click create button
  ![Alt text](images/image-7.png)

- Attach the internet gateway 'myVPC-IGW' to the vpc 'myVPC'
  ![Alt text](images/image-8.png)
  
```text
    - Actions ---> attach to VPC
    - Select VPC named "myVPC"
    - Push "Attach Internet gateway"
```

  ![Alt text](images/image-9.png)

### STEP 3 : Configuring Route Table and Create Route Table

- Go to the Route Tables from left hand menu

- rename the route table of the vpc 'myVPC' as 'myVPC-Public-Route-Table'
  ![Alt text](images/image-10.png)
- select Routes on the sub-section
- click edit routes
  ![Alt text](images/image-11.png)

- click add route

- add a route
  - destination ------> 0.0.0.0/0 (any network, any host)
  - As target;
    - Select Internet Gateway
    - Select 'myIGW'
    - save routes
  
    ![Alt text](images/image-12.png)
    ![Alt text](images/image-13.png)
    ![Alt text](images/image-14.png)

- show routes in the myVPC-Public-Route-Table
  ![Alt text](images/image-15.png)

- create a private route table (not allowing access to the internet)
  - name: 'myVPC-Private-Route-Table'
  - VPC : 'myVPC'
  - click create button
  ![Alt text](images/image-16.png)
  ![Alt text](images/image-17.png)
- show the routes in the route table myVPC-Private-Route-Table

- click Subnet association button and show the route table `myVPC-Private-Route-Table` with private subnets

### STEP 4: Create Subnets

- Go to the Subnets from left hand menu
- Push create subnet button
  ![Alt text](images/image-18.png)

- 1.
  Name tag          :myVPC-az1a-public-subnet
  VPC               :myVPC
  Availability Zone :us-east-1a
  IPv4 CIDR block   :172.24.1.0/24
  ![Alt text](images/image-19.png)

- 2.
  Name tag          :myVPC-az1a-private-subnet
  VPC               :myVPC
  Availability Zone :us-east-1a
  IPv4 CIDR block   :172.24.2.0/24
  ![Alt text](images/image-20.png)

- 3.
  Name tag          :myVPC-az1b-public-subnet
  VPC               :myVPC
  Availability Zone :us-east-1b
  IPv4 CIDR block   :172.24.4.0/24
  ![Alt text](images/image-21.png)

- 4.
  Name tag          :myVPC-az1b-private-subnet
  VPC               :myVPC
  Availability Zone :us-east-1b
  IPv4 CIDR block   :172.24.5.0/24
  
  ![Alt text](images/image-22.png)

- explain the subnet descriptions and reserved ips (why 251 instead of 256)
  - Because AWS has reserved 5 IP addresses, we cannot use these 5 IP addresses. That leaves only 256-5= 251 IP addresses available.  
  ![Alt text](images/image-23.png)

### STEP 5: Route Tables

- show the routes in the route table myVPC-Private-Route-Table

- click Subnet association button and show the route table `myVPC-Private-Route-Table` with private subnets
  ![Alt text](images/image-24.png)

- Click Edit subnet association
- Select private subnets;
  - az1a-private-subnet
  - az1b-private-subnet
  - and click save
  ![Alt text](images/image-25.png)

- Show the routes in the route table `myVPC-Public-Route-Table`

- Click Subnet association button and show the route table

- Show the default route table subnet association . There are only 2 subnet implicitly.

- myVPC-Public-Route-Table with public subnets

- Click Edit subnet association

- Select public subnets;
  - az1a-public-subnet
  - az1b-public-subnet
  - and click save
  ![Alt text](images/image-26.png)

### STEP 6: enable Auto-Assign Public IPv4 Address for public subnets

- Go to the Subnets from left hand menu

  - Select 'az1a-public-subnet' subnet ---> Action ---> Edit subnet settings  ---> select 'Enable auto-assign public IPv4 address' ---> Save
    ![Alt text](images/image-27.png)

    ![Alt text](images/image-28.png)

  - Select 'az1b-public-subnet' subnet ---> Action ---> Edit subnet settings  ---> select 'Enable auto-assign public IPv4 address' ---> Save


### STEP 7: Create Instances in your VPC

- Go to EC2 service. Launch two instances. One is in the Private and the other one is in Public subnet. Show the public and private IPs of instances.
  ![Alt text](images/image-29.png)
  ![Alt text](images/image-30.png)
  ![Alt text](images/image-31.png)
  ![Alt text](images/image-32.png)
  ![Alt text](images/image-33.png)
- Finally :
  - myVPC-Private-Instance 
  ![Alt text](images/image-34.png)

  - myVPC-Public-Instance
  ![Alt text](images/image-35.png)

