# Deploy a NEAR chunk validator on AWS
### A step-by-step guide

### Useful links:
- [StakeWars III](https://github.com/near/stakewars-iii)
- [Shardnet Wallet](https://wallet.shardnet.near.org/)
- [Shardnet Explorer](https://explorer.shardnet.near.org/)
- [Shardnet RPC](https://rpc.shardnet.near.org/status)
- [NEAR-CLI Docs](https://docs.near.org/docs/tools/near-cli)
- [NEAR Validator Bootcamp](https://near-nodes.io/validator/validator-bootcamp#starting-the-node-when-another-instance-is-running)

Before beginning, I highly recommend reading the following:

- [FAQ](https://github.com/near/stakewars-iii/blob/main/FAQ.md)
- [StakeWars Rules](https://github.com/near/stakewars-iii/blob/main/Rules.md)


## 1) Create a shardnet wallet

Use the [Shardnet Wallet](https://wallet.shardnet.near.org/) link and use the `Create Account` button.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179835560-21d7fd59-023f-4e8f-840e-d133a4019b17.png">


## 2)	Create an AWS account

We’ll be using AWS to host our validator. If you don’t have an existing AWS account, sign up for one [here](https://portal.aws.amazon.com/billing/signup#/start/email).

AWS provides a diverse set of on-demand cloud microservices. The company typically offers free tiers for most services but given that resource demands change based on your use case, a credit card is required at account initiation so keep it nearby.


## 3) Set up an EC2 instance 

Once your account is verified, you’ve stored your credentials in a safe place, and you’re logged in, use the search bar at the top of the AWS portal to query for the ‘EC2’ service – this will let us set up a containerized server instance for your node.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827698-b7c18bff-69b7-4a3a-9468-2b6670678cd8.png">

On the left-hand menu, select Instances and then select Launch Instances in the top right.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827736-59cd2bb3-c9c4-41b6-a0af-470b1e50d2e5.png">

Scroll until you find the Ubuntu Server 20.04 operating system and select this option.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827817-146e6959-c4f5-493a-9255-a9c21db266c2.png">

**__NOTE BEFORE PROCEEDING: it’s worth understanding that the required EC2 instance type (t2.xlarge) to run a node has a fixed cost per hour and this cost can depend on which region you are running the instance in. 500GB SSD storage adds an additional $47/month, for a total monthly cost of $185.49 to run an ‘on-demand’ t2.xlarge EC2 instance. There are options to ‘reserve’ instances for 1- or 3-year periods that lock you in to a lower monthly payment, but that configuration is beyond the scope of this guide.__**

For the US East (N. Virginia) region, the hourly costs are as follows:

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179827863-e651f566-1606-4fd9-bb56-4c3afddf7c45.png">

On the following menu, check the t2.xlarge instance type, as this will provide us with the necessary specifications to run a chunk validator (4-core CPU, 8GB of memory). Select Review and Launch.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828189-f7b73554-b0d6-4ffb-880a-98cf5ac9e4e5.png">

Next, scroll down to the Storage dropdown and select Edit storage. Once in the Add Storage menu, change the field in the Size column to 500 and then select Review and Launch. Then select Launch.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828564-bc9032b7-0a50-4da9-8fdf-1b65f590abcf.png">

Click Review and Launch, then Launch. Here you will be presented with a menu to create a new key pair by naming it (or select an existing key pair). Download the key pair file into an easily accessible directory as we will use this in the next step to securely login to our EC2 instance remotely through our computer’s terminal.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828669-a1df5a87-2afe-4e7a-ad2e-0c40abdef576.png">

Congratulations! You have just launched an EC2 instance capable of running a chunk validator on NEAR. After waiting a minute or so, your machine should have completed its checks and be shown under the Instances menu we visited earlier.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828719-a943712b-a86a-4e79-9efd-2efd82f8bfbf.png">


## 4) SSH into your EC2 instance

Check the checkbox next to your Instance ID in the Instances menu within EC2 and select Connect. Within the 'Connect to instance' window, follow the steps under the ‘SSH client’ section or following along with the simplified instructions I’ve laid out below.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/179828839-6b70f1aa-e2a9-4ddb-b4fa-00afdb1f7e64.png">

1. Open a new terminal and change the directory to the directory you stored your key pair in when launching your EC2 instance. If you’re uncertain of how to change directories look [here](https://www.rapidtables.com/code/linux/cd.html) (other useful unix commands that are worth learning include: pwd, ls, rm, rm -r, cd, and cd ..). E.g.: `cd /<path>/<key_pair_directory>`

2. In the ‘SHH client’ section copy and paste the command in #3 into your terminal and hit enter. E.g.: `chmod 400 <key_pair_name>.pem`

3. In the ‘SHH client’ section copy and paste the command in the provided Example into your terminal and hit enter. E.g.: `ssh -i "<key_pair_name>.pem " ubuntu@<public_DNS>`

4. If it asks for access permission, type ‘yes’ and hit enter.

You should now have remote access to an Ubuntu terminal on your EC2 instance! 

**NOTE: You may want to open a second (or third!) SSH session in a different terminal on your local machine to run commands while you're other terminal is occupied with the output of a running process. The steps are the same as we just covered.**


## 5) Setup your EC2 instance and install `near-cli`

The following steps are a streamlined version of those covered in [Challenge 1](https://github.com/near/stakewars-iii/blob/main/challenges/001.md)

Update your Ubunutu machine:
```
sudo apt update && sudo apt upgrade -y
```

Install `Node.js` and `npm`:
```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash - sudo apt install build-essential nodejs PATH="$PATH"
```

To check the `Node.js` and `npm` versions, run the following, respective commands:
```
node -v
```

```
npm -v
```

Install the NEAR-CLI:
```
sudo npm install -g near-cli
```

Set the environment to the correct network:
**NOTE: Each time you create a new SSH session, ensure that you set the environment to the correct network.**
```
export NEAR_ENV=shardnet
```

Check validator proposals for entering the validator set:
```
near proposals
```

Check active validators:
```
near validators current
```

Check validators entering the validator set in the next epoch:
```
near validators next
```


## 6) Install Rust, Nearcore, Access Your Wallet, and Start the Validator

The following steps are a streamlined version of those covered in [Challenge 2](https://github.com/near/stakewars-iii/blob/main/challenges/002.md)

We created a t2.xlarge EC2 instance which has the correct specifications (4-core CPU, 8GB RAM, and 500GB SSD) to run a NEAR chunk validator. If you want to check for yourself run in your terminal:

```
lscpu | grep -P '(?=.*avx )(?=.*sse4.2 )(?=.*cx16 )(?=.*popcnt )' > /dev/null && echo "Supported" || echo "Not supported"
```

Install the necessary developer tooling:
```
sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm cargo
```

Install Python pip:
```
sudo apt install python3-pip
```

Set the Python path configuration:
```
USER_BASE_BIN=$(python3 -m site --user-base)/bin
```

```
export PATH="$USER_BASE_BIN:$PATH"
```

Install the Building environment:
```
sudo apt install clang build-essential make
```

Install Rust and package manager Cargo:
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Set the environment:
```
source $HOME/.cargo/env
```

Clone `nearcore` from NEAR's Github repo:
```
git clone https://github.com/near/nearcore
```

```
cd nearcore
```

```
git fetch
```

Checkout the nearcore commit found in this [file](https://github.com/near/stakewars-iii/blob/main/challenges/commit.md):
```
git checkout <commit>
```

Compile nearcore:
**NOTE: Be patient as this will take several minutes at minimum.**
```
cargo build -p neard --release --features shardnet
```

Initialize the working directory for the necessary validator configuration files:
**NOTE: Ensure you are in the `/nearcore` directory.**
```
./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis
```
<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/180040230-e2ab8ad1-15e2-44e2-a9e6-85582346be97.png">


Replace the config.json file:
```
rm ~/.near/config.json
```

```
wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
```

Start the node to begin downloading headers and then blocks:
```
cd ~/nearcore
```

```
./target/release/neard --home ~/.near run
```

Once the blocks download to 100% login to your near account:
```
near login --walletUrl https://wallet.shardnet.near.org/
```

Since we don't have a GUI on our Ubuntu machine, copy and paste the link outputted by this command into your local browser and login to your shardnet accountId to grant access to the NEAR-CLI.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/180092627-7e8e6abf-2265-47e7-a2fa-660ded981af6.png">

Once the browsers loads to a refused to connect page, return to your SSH session and enter your shardnet accountId `xxxx.shardnet.near` under the line 'Enter it here'.

Generate your validator key, where `<pool_id>` is equivalent to `<pool_name>.factory.shardnet.near`:
```
near generate-key <pool_id>
```

Copy this generate file to the ~/.near directory that you initialized above, replacing `<wallet_name>` with your shardnet wallet name:
```
cp ~/.near-credentials/shardnet/<wallet_name>.shardnet.near.json ~/.near/validator_key.json
```

Use the command `vim` to edit `validator_key.json`:
```
vim ~/.near/validator_key.json
```

1. In vim, hit `i` for `insert`, and replace the `private_key` key value with `secret_key`. Your file structure should similar to this:

```
{
  "account_id": "xxxx.factory.shardnet.near",
  "public_key": "ed25519:HeaBJ3xLgvZacQWmEctTeUqyfSU4SDEnEwckWxd92W2G",
  "secret_key": "ed25519:****"
}
```

2. Hit `esc` to exit the insert, `:` then `wq` to save and quit.

Start the validator daemon:
```
target/release/neard run
```

Use a second SSH session or quit the terminal `ctrl + c` and run the following to setup the Systemd Command:
```
sudo vi /etc/systemd/system/neard.service
```

Use vim and the commands we learned above to edit this file, replacing each instance of `user` with `ubuntu`:
```
[Unit]
Description=NEARd Daemon Service

[Service]
Type=simple
User=<USER>
#Group=near
WorkingDirectory=/home/<USER>/.near
ExecStart=/home/<USER>/nearcore/target/release/neard run
Restart=on-failure
RestartSec=30
KillSignal=SIGINT
TimeoutStopSec=45
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

Enable neard and start neard:
```
sudo systemctl enable neard

sudo systemctl start neard
```

Download ccze to prettify your output:
```
sudo apt install ccze
```

Run your prettified logs:
```
journalctl -n 100 -f -u neard | ccze -A
```
<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/180086916-be366afa-4b85-49cb-ad1a-523cf344a3fb.png">


## 7) Mount a Staking Pool, Depositing and Staking NEAR, and Pinging Your Validator

The following steps are a streamlined version of those covered in [Challenge 3](https://github.com/near/stakewars-iii/blob/main/challenges/003.md)

Deploy a new staking pool:
```
near call factory.shardnet.near create_staking_pool '{"staking_pool_id": "<pool id>", "owner_id": "<accountId>", "stake_public_key": "<public key>", "reward_fee_fraction": {"numerator": 5, "denominator": 100}, "code_hash":"DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ"}' --accountId="<accountId>" --amount=30 --gas=300000000000000
```

- staking_pool_id: use the `<pool_id>` value before factory in your full account_id `<pool_id>.factory.shardnet.near`
- owner_id: use your full shardnet wallet name, e.g.: `xxxx.shardnet.near`
- stake_public_key: use the value found in the `validatory_key.json` file in `~/.near`
- account_id: same value as `owner_id`
  
Once that command is entered correctly, you should receive a `true` response back.
  
Commands for depositing and staking, unstaking, and unstaking all NEAR:

**NOTE: You must deposit and stake sufficient NEAR to cover the validator Seat Price or your node will not be included in the next validator set. You can find the current Seat Price here: [Shardnet Explorer](https://explorer.shardnet.near.org/)**
  
<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/180091780-732887d1-5d72-40e4-bc01-d2e7ba79415b.png">
  
```
near call <staking_pool_id> deposit_and_stake --amount <amount> --accountId <accountId> --gas=300000000000000
 
near call <staking_pool_id> unstake '{"amount": "<amount yoctoNEAR>"}' --accountId <accountId> --gas=300000000000000
  
near call <staking_pool_id> unstake_all --accountId <accountId> --gas=300000000000000
```

Pinging is necessary at each epoch to ensure reported rewards are current:

```
near call <staking_pool_id> ping '{}' --accountId <accountId> --gas=300000000000000
```
  
Check [Shardnet Explorer](https://explorer.shardnet.near.org/) for your validator - it should be in a Proposal state awaiting to join the validator set in the next epoch. Congratulations, you have just set up a NEAR chunk validator!

<img width="750" alt="image" src="https://user-images.githubusercontent.com/34758484/180092940-0469661f-eec2-4ff3-9132-b986d3cd8120.png">


