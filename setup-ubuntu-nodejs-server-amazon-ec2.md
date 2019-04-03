# Setup Ubuntu Server in Amazon EC2

Our server will have this configuration:
- Programs
  - Nodejs
  - MongoDB
- Sudoer user: ubuntu
- User for Webapp: nodejs

## Index
- [Creation](#creation)
- [SetUp](#setup)

## Creation

### Step 1
  Go to [EC2 Management Console](https://console.aws.amazon.com/ec2/v2/home) go to Instances -> Launch Instance
  ![photo](./images/setup-ubuntu-server/photo-1.png?raw=true)

### Step 2
  Select the Ubuntu Server in this case we will use Ubuntu Server 14.04
  ![photo](./images/setup-ubuntu-server/photo-2.png?raw=true)
  Select the instance type, instance details, storage

### Step 3
  Always add a tag name
  ![photo](./images/setup-ubuntu-server/photo-3.png?raw=true)

  Configure the security group, make sure to open the ssh port (22) and the port of your Nodejs process (ie: `8080`)
  ![photo](./images/setup-ubuntu-server/photo-4.png?raw=true)

### Step 4
  Create and download a key pair for ssh connections
  ![photo](./images/setup-ubuntu-server/photo-5.png?raw=true)

Now we have the new instance of the server done. You can connect via ssh to the server using this command in your terminal
```bash
ssh -i mypair.pem ubuntu@myserverip
```

_NOTE: replace __mypair.pem__ with the path of the file you download in step 4, replace __myserverip__ with the ip of the server_

## SetUp

Connect via ssh to the server.
You need to add your ssh public key in to authorized keys of the user ubuntu. (see [how to create a ssh key](https://help.github.com/articles/generating-an-ssh-key/))

Edit the file __~/.ssh/authorized_keys__:
```bash
nano ~/.ssh/authorized_keys
```

Paste the content of your __id_rsa.pub__ on the last line. Type ctrl-x to exit and save the file.
Now you can connect to the server without the amazon pem using `ssh ubuntu@myserverip`

#### Step 1
We need to install the last version of Nodejs:
```bash
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

_NOTE: in this tutorial I'm installing the current LTS version. To install another version just replate `setup_10.x` with `setup_{mayor version}.x`

Install [PM2](https://pm2.io/runtime/) __(Optional)__:
```bash
sudo npm i -g pm2
```

#### Step 2
Install MongoDB, if your project requires.

Import the public key used by the package management system.
```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
```
Create a list file for MongoDB
```bash
echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

_NOTE: in this tutorial I'm installing the current LTS version. To install another version just replate `mongodb-org/3.4` and `mongodb-org-3.4.list` with `mongodb-org/{current version}` and `mongodb-org-{current version}.list`

Reload local package database
```bash
sudo apt-get update
```

Install MongoDB:
```bash
sudo apt-get install -y mongodb-org
```

#### Step 3
For security you need to create a new user to run the nodejs process.
```bash
sudo useradd nodejs -m -s /bin/bash
```
Now you can login as nodejs and add your ssh public key in to authorized_keys
```bash
sudo su nodejs
mkdir ~/.ssh
nano ~/.ssh/authorized_keys
```
Paste the content of your __id_rsa.pub__ on the last line. Type ctrl-x to exit and save the file.
Now you can connect to the server without the amazon pem using `ssh nodejs@myserverip`
