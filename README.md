# Deploy a NEAR chunk validator on AWS
## Step-by-step guide

### Useful links:
[Shardnet Challenges](https://github.com/near/stakewars-iii/blob/main/challenges/001.md)
[Shardnet Wallet](https://wallet.shardnet.near.org/)
[Shardnet RPC](https://rpc.shardnet.near.org/status)
[NEAR-CLI Docs](https://docs.near.org/docs/tools/near-cli)
[NEAR Validator Bootcamp](https://near-nodes.io/validator/validator-bootcamp#starting-the-node-when-another-instance-is-running)


## 1)	Create an AWS account
We’ll be using AWS to host our validator. If you don’t have an existing AWS account, sign up for one [here](https://portal.aws.amazon.com/billing/signup#/start/email).

AWS provides a diverse set of on-demand cloud microservices. The company typically offers free tiers for most services but given that resource demands change based on your use case, a credit card is required at account initiation so keep it nearby.


## 2) Set up and EC2 instance 

Once your account is verified, you’ve stored your credentials in a safe place, and you’re logged in, use the search bar at the top of the AWS portal to query for the ‘EC2’ service – this will let us set up a containerized server instance for your node.

<img width="470" alt="image" src="https://user-images.githubusercontent.com/34758484/179827698-b7c18bff-69b7-4a3a-9468-2b6670678cd8.png">

On the left-hand menu, select Instances and then select Launch Instances in the top right.

<img width="468" alt="image" src="https://user-images.githubusercontent.com/34758484/179827736-59cd2bb3-c9c4-41b6-a0af-470b1e50d2e5.png">

Scroll until you find the Ubuntu Server 20.04 operating system and select this option.![image](https://user-images.githubusercontent.com/34758484/179827808-799f5247-9c37-4a17-bcb0-89f5e91d2db0.png)

<img width="470" alt="image" src="https://user-images.githubusercontent.com/34758484/179827817-146e6959-c4f5-493a-9255-a9c21db266c2.png">

**NOTE BEFORE PROCEEDING**: it’s worth understanding that the required EC2 instance type (t2.xlarge) to run a node has a fixed cost per hour and this cost can depend on which region you are running the instance in.

For the US East (N. Virginia) region, the hourly costs are as follows:
<img width="470" alt="image" src="https://user-images.githubusercontent.com/34758484/179827863-e651f566-1606-4fd9-bb56-4c3afddf7c45.png">
500GB SSD storage adds an additional $47/month, for a total monthly cost of $185.49 to run an ‘on-demand’ t2.xlarge EC2 instance. There are options to ‘reserve’ instances for 1- or 3-year periods that lock you in to a lower monthly payment, but that configuration is beyond the scope of this guide.!

On the following menu, check the t2.xlarge instance type, as this will provide us with the necessary specifications to run a chunk validator (4-core CPU, 8GB of memory). Select Review and Launch.

<img width="470" alt="image" src="https://user-images.githubusercontent.com/34758484/179828189-f7b73554-b0d6-4ffb-880a-98cf5ac9e4e5.png">

Next, scroll down to the Storage dropdown and select Edit storage. Once in the Add Storage menu, change the field in the Size column to 500 and then select Review and Launch. Then select Launch.


