# AWS VPC Peering          
            
1. Go to the AWS Console -> Select Region - `Ohio`
  
 
2. In EC2 -> Open VPC, you will have a default VPC -> Create VPC   

    Name - `test-vpc`
    
    IPv4 - `10.0.0.0/16`
    
    Click on Create VPC


3. In the VPC page click on Subnets -> Create subnet -> VPC ID - Select the already created `test-vpc`

    Subnet name - `test-public-subnet`
    
    Availability Zone - `us-east-2a`
    
    IPv4 subnet CIDR block - `10.0.0.0/24`
    
    Click on Create subnet
    

4. Now go to EC2 -> Launch instance -> Name - `test-instance` -> Select Ubuntu , t2 micro and a keypair -> In Network settings click on Edit -> Select the VPC - `test-vpc` which automatically assigns the Subnet 

    Auto-assign public IP - Enable 
    
    Enable Create a security group 
    
    Add an Inbound Security Group Rule Type - `HTTP` and Source type - `Anywhere` 
    
    Click on Launch instance


5. Now check the EC2 SSH connection which will not establish the connection as the instance is in a Private Subnet  

![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/1ee9f6be-17a3-4cc3-8051-fd7403d957d7)


6. Now go to the VPC dashboard -> Click on Internet gateways -> Click on Create internet gateway

    Name tag - `test-igw`
    
    Click on Create internet gateway
    
    Now click on Actions -> Attach to VPC in the internet gateway page -> Select the already created `test-vpc` -> Click on Attach internet gateway 
    

7. Now go to Route tables -> Click on Create route table 

    Name - `test-rt`
    
    VPC  - `test-vpc`
    
    Click on Create route table


8.  In the Route table page -> Click on Subnet association section -> Click on Edit subnet associations -> Select the `test-public-subnet` -> Click on Save associations

    Now in the same Route table page -> Click on Edit routes -> Add route -> `0.0.0.0/0` and Target - `Internet Gateway` -> Click on Save changes


9. Now check the EC2 SSH connection which will establish the connection as the instance is in a Public Subnet   

![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/da825adf-1f7c-49f2-8b68-509f7c234739)


10. Similarly create another VPC called `prod-vpc` with CIDR as `192.168.0.0/16` with prod-subnet as `192.168.0.0/24` with `prod-igw` with `prod-rt` with `prod-instance` with CIDR `192.168.0.0/16` by following Step 2. to Step 9.


11. Try to Ping from test-instance to prod-instance which will not ping

![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/a7ff1d02-1fd7-4b2f-8ae7-567a20b04aaf)


![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/d6ae35d4-1812-4ec6-bca1-63cb933eb94d)


12. Now go to the VPC dashboard -> Click on the Peering connections -> Click on Create peering connection 
  
    Name - `test-prod-peering`
    
    VPC ID (Requester) - `test-vpc`
    
    VPC ID (Accepter) - `prod-vpc`
    
    Click on Create peering connection
    
    Click on Actions -> Accept request

    Now go back to the Route tables -> Click on `test-rt` -> Click on Edit routes -> `192.168.0.0/16` and Target - `Peering Connection` -> Click on Save changes
    
    Similarly, Now go back to the Route tables -> Click on `prod-rt` -> Click on Edit routes -> `10.0.0.0/16` and Target - `Peering Connection` -> Click on Save changes
    
    Now go the test-instance -> Click on Security -> Select the Security group -> Click on Edit inbound rules -> Click on Add rule -> `All ICMP - IPv4` and Source - Custom - `192.168.0.0/16` -> Click on Save rules 
    
    Now go the prod-instance -> Click on Security -> Select the Security group -> Click on Edit inbound rules -> Click on Add rule -> `All ICMP - IPv4` and Source - Custom - `10.0.0.0/16` -> Click on Save rules 

![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/a73aed06-e759-41f0-9b9c-8eb3d3768e43)

![image](https://github.com/Pavan-1997/AWS_VPC_Peering/assets/32020205/c81ef6df-a7cf-4108-93a1-ff06de7f6ddd)

-----

13. Go to the VPC dashboard -> Click on Subnets -> Create subnet -> Select the already created (test-vpc)

    Subnet name - `test-private-subnet`
    
    Availability Zone - `us-east-2b`
    
    IPv4 subnet CIDR block - `10.0.1.0/24`
    
    Click on Create Subnet


14. Now go to the NAT gateways -> Click on Create NAT gateway  

    Name - `test-nat`
    
    Subnet - `test-public-subnet`
    
    Click on Allocate Elastic IP 
    
    Click on Create NAT gateway


15. Now go to the Route tables -> Click on `test-rt` -> Click on Edit routes -> Add route -> `10.0.1.0/24` and Target - `NAT Gateway` -> Click on Save changes


