# Three tier student app setup
## VPC Setup
- **Create VPC**
- Go to AWS Console -> go to VPC
- click on "create VPC"
   - Resource to create: VPC only
   - name: vpc-three-tier
   - IPv4 CIDR: 192.168.0.0/16
   - create vpc
- **Subnet Creation**
- create subnet
- VPC ID: select vpc-three-tier
- Subnet-1
   - name: public-subnet-nginx
   - Availability Zone (AZ) : ap-southest-1a
   - IPv4 VPC CIDR block:  192.168.0.0/16
   - IPv4 subnet CIDR block :192.168.0.0/23
- subnet-2
   - name: private-subnet-tomcat
   - IPv4 VPC CIDR block:  192.168.0.0/16
   -  IPv4 subnet CIDR block :192.168.2.0/23
- subnet-3
   - name: private-subnet-database
   - IPv4 VPC CIDR block:  192.168.0.0/16
   -  IPv4 subnet CIDR block :192.168.4.0/23
- click on "create subnet"
- **Internet Gateway Creation**
   -  name: igw-three-tier-project
   -  clikc on "create internet gateway"
   -  attach "vpc-three-tier" VPC.
- **NAT Gateway Creation**
   - name: nat-three-tier
   - subnet: public-subnet-nginx
   - Elastic IP: allocate elastic IP
   - click on "create NAT gateway"
- VPC Resource Map
![Screenshot 2025-06-24 211640](https://github.com/user-attachments/assets/4978a00d-3da3-4873-9ddb-8b681b0a2743)
- **Create Security Group**
- name: three-tier-project-sec
- Description: allow traffic for project
- VPC: vpc-three-tier
- edit inbound rule, allow following traffic
   - ssh  : 22 : anywhere-IPv4
   - http : 80 :  anywhere-IPv4
   - MYSQL/Arora : 3306 : anywhere-IPv4
   - custom TCP : 8080 : anywhere-IPv4
- click on "create Security group"

## EC2 Setup
- go to EC2 to launch instances
- **Instance-1**
   - name: public-nginx-server
   - AMI: Amazon linux
   - instacne type : tc.micro
   - key pair : create new kep pair three-tier.pem
   - VPC : vpc-three-tier
   - subnet : public-subnet-nginx
   - auto assign public IP : enabel
   - security group : three-tier-project-sec
   - launch instance
- **Instance-2**
   - name: private-tomcat-server
   - AMI: Amazon linu
   - instance type: t2.micro
   - key pair: three-tier.pem
   - VPC: vpc-three-tier
   - subnet: private-subnet-tomcat
   - auto assign public IP : disable
   - security group: three-tier-project-sec
   - launch instance
- **Instance-3**
   - name: private-database-server
   - AMI: Amazon linu
   - instance type: t2.micro
   - key pair: three-tier.pem
   - VPC: vpc-three-tier
   - subnet: private-subnet-database
   - auto assign public IP : disable
   - security group: three-tier-project-sec
   - launch instance 
## Database creation
- create Database in RDS
- database creation method : standard create
- engine option: MariaDB
- tamplates: free tier
- DB instance identifier: database-1
- Master username: admin
- Credentials management: self managed
- master password: Passwd123$ (you can assign your own password)
- Connectivity: Connect to an EC2 compute resource
- EC2 instance: select "privat-database-server"
- DB subnet group : Automatic setup
- VPC security group (firewall): Choose existing
- Additional VPC security group: three-tier-project-sec
- click on "create database"

## Connect to public-nginx-serve
- connect to instnace
- change hostname
```
sudo hostname nginx
```
```
bash
```
- create key pair, so we can access private instnaces
- go to downloads in your local machine, copy three-tier.pem file content
- create new file in the instance and paste here
```
suddo vim three-tier.pem
```
```
chmod 400 three-tier.pem
```
- now ssh into database sever
```
ssh -i three-tier.pem ec2-user@<ip_of _database_server>
```
- change hostname
```
sudo hostname database
```
```
bash
```
- install mariaDB
```
sudo yum install mariadb105-server -y
```
```
sudo systemctl start mariadb
```
```
sudo systemctl enable mariadb
```
- login to database
- go to database , cpoy the endpoint of database
```
mysql -h rds-endpoint   -u admin -pPasswd123$
```
- note- replace rds-endpoint with actual endpoint
![Screenshot 2025-06-24 223443](https://github.com/user-attachments/assets/44f90a30-878c-40a1-b40a-458912855193)
- run following commands
```
show databases;
```
```
create database  studentapp;
```
```
use studentapp;
```
```
 CREATE TABLE if not exists students(student_id INT NOT NULL AUTO_INCREMENT,  
	student_name VARCHAR(100) NOT NULL,  
	student_addr VARCHAR(100) NOT NULL,   
	student_age VARCHAR(3) NOT NULL,      
	student_qual VARCHAR(20) NOT NULL,     
	student_percent VARCHAR(10) NOT NULL,   
	student_year_passed VARCHAR(10) NOT NULL,  
	PRIMARY KEY (student_id)  
);
```
```
show tables;
```
- logoout from database
```
exit
```
- logout from database server
```
exit
```
- log in to tomcat server
```
ssh -i three-tier.pem ec2-user@<ip_of_tomcat_server>
```
- change the hostname
```
sudo hosnamt tomcat
```
```
bash
```
```
sudo -i
```

- install java
```
 yum install java -y
```
- install tomact
```
curl -O https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.105/bin/apache-tomcat-9.0.105.tar.gz
```
```
tar -xzvf apache-tomcat-9.0.105.tar.gz -C /opt/
```
- go to webapps directory and download ``.war`` file
```
cd /opt
```
```
cd apache-tomcat-9.0.105
```
```
cd webapps
```
```
 curl -O https://s3-us-west-2.amazonaws.com/studentapi-cit/student.war
```
```
cd ..
```
```
cd /lib
```
```
curl -O https://s3-us-west-2.amazonaws.com/studentapi-cit/mysql-connector.jar
```
- Modify context.xml file
```
cd ..
```
```
cd /opt
```
```
cd apache-tomcat-9.0.105
```
```
cd conf
```
```
vim context.xml
```
- add following lines st line 20 below th context
```
 <Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxTotal="100" maxIdle="30" maxWaitMillis="10000"
               username="admin" password="Passwd123$" driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://DB-ENDPOINT:3306/DATABASE-studentapp"/>
```
![Screenshot 2025-06-24 233536](https://github.com/user-attachments/assets/0ac38508-8e4a-4f2e-8f95-bd402ccf5e01)
```
cd ..
```
```
cd bin
```
```
chmod +x catalina.sh
```
```
./catalina.sh start
```
- Back ot public-nginx-server
```
sudo -i
```
- install nginx
```
yum install nginx -y
```
```
 vim /etc/nginx/nginx.conf
```
- set nu
- add following lines at line 49 below loaction
```
location / {
proxy_pass http://private-IP-tomcat:8080/student/;
}
```
![Screenshot 2025-06-24 234755](https://github.com/user-attachments/assets/897d6215-a6d3-4caf-abcf-a67d117eb70a)

```
systemctl start nginx
```
- go to browser hit the public IP of nginx-server

![Screenshot 2025-06-25 000641](https://github.com/user-attachments/assets/196548fe-edfc-4b34-abe6-85fa55e04ef6)
![Screenshot 2025-06-25 000722](https://github.com/user-attachments/assets/b8552116-7354-4df1-8b14-ed38ea4298ec)

![Screenshot 2025-06-25 000549](https://github.com/user-attachments/assets/2d9eb9e8-8c7f-4e3f-b76f-a90c1cce0bd9)









 
