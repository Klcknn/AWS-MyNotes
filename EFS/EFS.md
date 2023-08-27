# Hands-on EFS:

## Outline

- Part 1 - Prep(EC2 SecGrp, EFS SecGrp, EC2 Linux 2 Instance)

- Part 2 - Creating EFS

- Part 3 - Attach the EFS to the multiple EC2 Linux 2 instances


## Part 1 - Prep (EC2 SecGrp, EFS SecGrp, EC2 Linux 2 Instance)

### Step 1 - Create EC2 SecGrp:

- Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

- Choose the Security Groups on left-hand menu

- Click the `Create Security Group`.
- ![EC2-SG](images/image.png)

```text
Security Group Name  : EC2-SecGrp
Description          : SecGrp for EC2
VPC                  : Default VPC
Inbound Rules:
- Type: SSH ----> Source: 0.0.0.0/0
Outbound Rules: Keep it as default
Tag:
    - Key   : Name
      Value : EC2-SecGrp
```
![Alt text](images/image-2.png)
![Alt text](images/image-3.png)
![Alt text](images/image-4.png)

### Step 2 - Create EFS SecGrp:

- Click the `Create Security Group`.
- **NFS(Network File Sharing)** ***2049*** dosya erişimi icin kullanılır.

```text
Security Group Name  : EFS-SecGrp
Description          : SecGrp for EFS
VPC                  : Default VPC 
Inbound Rules:
- Type: NFS ----> Port: 2049 ------>Source: Custom: EC2-SecGrp # Yukarida olusturmuş oldugumuz security group  atıyoruz buraya
Outbound Rules: Keep it as default
Tag:
    - Key   : Name
      Value : EFS-SecGrp


```

![Alt text](images/image-5.png)
![Alt text](images/image-6.png)
![Alt text](images/image-7.png)

### Step 3 - Create EC2:

- Configure First Instance in N.Virginia

```text
AMI             : Amazon Linux 2
Instance Type   : t2.micro
Network         : default
Subnet          : Avaibility Zone: us-east-1a (optional) I prefered
Security Group  : EC2-SecGrp
    Sec.Group Name : EC2-SecGrp
Tag             :
    Key         : Name
    Value       : EC2-First-Instance
```

- Configure Second Instance in N.Virginia

```text
AMI             : Amazon Linux 2
Instance Type   : t2.micro
Network         : default
Subnet          : default
Security Group  : EC2-SecGrp
    Sec.Group Name : EC2-SecGrp
Tag             :
    Key         : Name
    Value       : EC2-Second-Instance
```
![Alt text](images/image-9.png)
![Alt text](images/image-10.png)
![Alt text](images/image-11.png)
![Alt text](images/image-12.png)
![Alt text](images/image-13.png)
![Alt text](images/image-14.png)

- Finally ;
- ![Alt text](images/image-15.png)

## Part 2 - Creating EFS

- Open the Amazon EFS console at https://console.aws.amazon.com/efs/.
- Click "Create File System" 
  
- ![Alt text](images/image-16.png)
- ![Alt text](images/image-17.png)

```text

Name                            : MyEFS
Virtual Private Cloud (VPC)     : Default VPC (Keep default)
Availability and Durability     : Regional (Keep default)
```

- To customize settings manually, select the "Customize" option seen at the bottom 

```text

General

Name                    : MyEFS (Comes default from previous setting)

Availability and Durability : Regional (Comes default from previous setting)

Automatic backups       : Unchecked "Enable automatic backups"

Lifecycle management    : Select "None"

Performance mode        : General Purpose

Throughput mode         : Bursting

Encryption              : Enable encryption of data at rest

Tags                    : optional

Click Next
```

![Alt text](images/image-18.png)
![Alt text](images/image-19.png)

```text
- Virtual Private Cloud (VPC): Default VPC
- Mount targets: 
  - select all AZ (keep it default)
  - Clear "default sg" from all AZ
  - Add "EFS-SecGrp" to all AZ
- Show that you can only add one mount point for each AZ though it has multiple subnets(for example custom VPC) 
```

![Alt text](images/image-20.png)

- Click next 

```text
File system policy - optional------> keep it as is
```

![Alt text](images/image-21.png)

- Click next. Then review and Create 

- Show that it is created. 

## Part 3 - Attach the EFS to the multiple EC2 Linux 2 instances

### STEP-1: Configure the EC2-1 instance

- open EC2 console
![Alt text](images/image-22.png)
- Connect to EC2-1 with SSH.

```text
ssh -i .....pem ec2-user@..................
```

![Alt text](images/image-23.png)
![Alt text](images/image-24.png)

- Update the installed packages and package cache on your instance.

```text
sudo yum update -y
```

- Install the "Amazon-efs-utils Package" on Amazon Linux

```text
sudo yum install -y amazon-efs-utils
```

- Create Mounting point 

```text
sudo mkdir efs
```

![Alt text](images/image-25.png)

- Go to the EFS console and click  on "MyEFS" . Then click "Attach" button seen top of the "EFS" page.
- ![Alt text](images/image-26.png)
- ![Alt text](images/image-27.png)

- On the pop up window, copy the script seen under "Using the EFS mount helper" option: "sudo mount -t efs -o tls fs-60d485e2:/ efs"

- ![Alt text](images/image-28.png)

- Turn back to the terminal and mount EFS using the "EFS mount helper" to the "efs" mounting point

```text
sudo mount -t efs -o tls fs-xxxxxx:/ efs
```

- Check the "efs" folder

```text
ls
```

- Go the "efs" folder and create a new file with Nano editor.

```text
cd efs
sudo nano deneme.txt
```

- Write something, save and exit;

```text
"This is EC2-First-Instance"
```

![Alt text](images/image-29.png)

- check the deneme.txt

```text
cat deneme.txt
```

### STEP-2: Configure the EC2-2 instance

- Connect to EC2-2 with SSH.
- ![Alt text](images/image-30.png)

- Change hostname of the EC2 instance and Colorize Terminal instance
  
```text
sudo hostnamectl set-hostname SecondEC2
bash
PS1="\[\e[1;33m\]\u\[\e[35m\]@\h# \W:\[\e[32m\]\\$\[\e[m\]"
```

![Alt text](images/image-31.png)

```text
ssh -i .....pem ec2-user@..................
```

- Update the installed packages and package cache on your instance.

```text
sudo yum update -y
```

- Install the "Amazon-efs-utils Package" on Amazon Linux

```text
sudo yum install -y amazon-efs-utils
```

![Alt text](images/image-32.png)

- Create Mounting point 

```text
sudo mkdir efs
```

- Go to the EFS console and click  on "MyEFS" . Then click "Attach" button seen top of the "EFS" page.

- On the pop up window, copy the script seen under "Using the EFS mount helper" option: "sudo mount -t efs -o tls fs-60d485e2:/ efs"

- Turn back to the terminal and mount EFS using the "EFS mount helper" to the "efs" mounting point

```text
sudo mount -t efs -o tls fs-xxxxxxx:/ efs
```

- Check the "efs" folder

```text
ls
```

- Check the hello.txt. Show that you can also reach the same file.

```text
cat deneme.txt
```

- Add something deneme.txt

```text
sudo nano deneme.txt
"This is EC2-Second-Instance"
```

![Alt text](images/image-34.png)
![Alt text](images/image-33.png)

- Check the deneme.txt

```text
cat deneme.txt

"This is EC2-First-Instance"
"This is EC2-Second-Instance"
```

- Connect from EC2-1 to the "efs" and show the deneme.txt:

```text
cd efs
cat deneme.txt

"This is EC2-First-Instance"
"This is EC2-Second-Instance"
```

![Alt text](images/image-35.png)

### STEP-3: Configure the EC2-3 instance with EFS while Launching

- go to the EC2 console and click "Launch Instance"

- Configure third Instance in N.Virginia

```text
AMI             : Amazon Linux 2
Instance Type   : t2.micro
Network         : default
**File systems  :Add file system-------> myEFS (Note down the mnt point "/mnt/efs/fs1")
Subnet          : default
Security Group  : InstancesSecGrp
    Sec.Group Name :  SecGrp for EC2
Tag             :
    Key         : Name
    Value       : EC2-3
```

![Alt text](images/image-43.png)
![Alt text](images/image-44.png)
![Alt text](images/image-45.png)
![Alt text](images/image-46.png)
![Alt text](images/image-47.png)
![Alt text](images/image-48.png)

- Connect to EC2-3 with SSH

```text
ssh -i .....pem ec2-user@..................
```

- Go to the directory of mount target 

```text
cd /mnt/efs/fs1/
```

- Show the deneme.txt:

```text
cat deneme.txt

"This is EC2-1-Instance"
"This is EC2-2-Instance"
```

- Add something deneme.txt

```text
sudo nano deneme.txt
"This is EC2-3-Instance"
```

- Check the deneme.txt

```text
cat deneme.txt

"This is EC2-1-Instance"
"This is EC2-2-Instance"
"This is EC2-3-Instance"
```

![Alt text](images/image-36.png)

- Terminate instances and delete file system from console.

- **Shortly Note:**
- Create backups EFS into instances

```text
sudo reboot now
sudo cp /etc/fstab /etc/fstab.bak
sudo nano /etc/fstab
fs-08dbce00b51780648:/ /home/ec2-user/efs  efs  defaults,_netdev   0    0
```

![Alt text](images/image-39.png)

![Alt text](images/image-38.png)

![Alt text](images/image-40.png)

![Alt text](images/image-41.png)

At Finally ;

![Alt text](images/image-42.png)