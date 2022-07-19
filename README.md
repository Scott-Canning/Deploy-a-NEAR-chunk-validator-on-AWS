# Deploy a NEAR chunk validator on AWS
### Step-by-step guide

### Useful links:
- [Shardnet Challenges](https://github.com/near/stakewars-iii/blob/main/challenges/001.md)
- [Shardnet Wallet](https://wallet.shardnet.near.org/)
- [Shardnet RPC](https://rpc.shardnet.near.org/status)
- [NEAR-CLI Docs](https://docs.near.org/docs/tools/near-cli)
- [NEAR Validator Bootcamp](https://near-nodes.io/validator/validator-bootcamp#starting-the-node-when-another-instance-is-running)


## 1)	Create an AWS account
We’ll be using AWS to host our validator. If you don’t have an existing AWS account, sign up for one [here](https://portal.aws.amazon.com/billing/signup#/start/email).

AWS provides a diverse set of on-demand cloud microservices. The company typically offers free tiers for most services but given that resource demands change based on your use case, a credit card is required at account initiation so keep it nearby.


## 2) Set up and EC2 instance 

Once your account is verified, you’ve stored your credentials in a safe place, and you’re logged in, use the search bar at the top of the AWS portal to query for the ‘EC2’ service – this will let us set up a containerized server instance for your node.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827698-b7c18bff-69b7-4a3a-9468-2b6670678cd8.png">

On the left-hand menu, select Instances and then select Launch Instances in the top right.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827736-59cd2bb3-c9c4-41b6-a0af-470b1e50d2e5.png">

Scroll until you find the Ubuntu Server 20.04 operating system and select this option.![image](https://user-images.githubusercontent.com/34758484/179827808-799f5247-9c37-4a17-bcb0-89f5e91d2db0.png)

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827817-146e6959-c4f5-493a-9255-a9c21db266c2.png">

**NOTE BEFORE PROCEEDING**: it’s worth understanding that the required EC2 instance type (t2.xlarge) to run a node has a fixed cost per hour and this cost can depend on which region you are running the instance in.

For the US East (N. Virginia) region, the hourly costs are as follows:

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827863-e651f566-1606-4fd9-bb56-4c3afddf7c45.png">

500GB SSD storage adds an additional $47/month, for a total monthly cost of $185.49 to run an ‘on-demand’ t2.xlarge EC2 instance. There are options to ‘reserve’ instances for 1- or 3-year periods that lock you in to a lower monthly payment, but that configuration is beyond the scope of this guide.!

On the following menu, check the t2.xlarge instance type, as this will provide us with the necessary specifications to run a chunk validator (4-core CPU, 8GB of memory). Select Review and Launch.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828189-f7b73554-b0d6-4ffb-880a-98cf5ac9e4e5.png">

Next, scroll down to the Storage dropdown and select Edit storage. Once in the Add Storage menu, change the field in the Size column to 500 and then select Review and Launch. Then select Launch.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828564-bc9032b7-0a50-4da9-8fdf-1b65f590abcf.png">

Click Review and Launch, then Launch. Here you will be presented with a menu to create a new key pair by naming it (or select an existing key pair). Download the key pair file into an easily accessible directory as we will use this in the next step to securely login to our EC2 instance remotely through our computer’s terminal.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828669-a1df5a87-2afe-4e7a-ad2e-0c40abdef576.png">

Congratulations! You have just launched an EC2 instance capable of running a chunk validator on NEAR. After waiting a minute or so, your machine should have completed its checks and be shown under the Instances menu we visited earlier.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828719-a943712b-a86a-4e79-9efd-2efd82f8bfbf.png">


## 3) SSH into your EC2 instance

Check the checkbox next to your Instance ID in the Instances menu within EC2 and select Connect. Within the Connect to Instance window, follow the steps under ‘SSH Client’ or following along with the simplified instruction I’ve laid out below.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828839-6b70f1aa-e2a9-4ddb-b4fa-00afdb1f7e64.png">

1. Open a new terminal and change the directory to the directory you stored your key pair in when launching your EC2 instance. E.g., `cd /<path>/<key_pair_directory>`

If you’re uncertain of how to change directories, Google how to change directories in your UNIX shell via the command line (other useful commands to learn include: pwd, ls, rm, rm -r, cd, and cd ..).

2. In the ‘SHH Client’ section copy and paste the command in #3 into your terminal and hit enter. E.g., `chmod 400 <key_pair_name>.pem`

3. In the ‘SHH Client’ section copy and paste the command in the provided Example into your terminal and hit enter. E.g., `ssh -i "<key_pair_name>.pem " ubuntu@<public_DNS>`

4. If it asks for access permission, type ‘yes’ and enter


## 3) Setup your EC2 instance and install `near-cli`
