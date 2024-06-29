# Node.js Express App Deployment on AWS EC2 with CodePipeline and CodeDeploy

In this guide, I'll walk you through the steps to deploy a Node.js for my application. 

## Objective

The goal of this project was to automate the deployment process of a Node.js for my application from GitHub to an AWS EC2 instance.I was able to:

- Set up a GitHub repository for my application.
- Configure AWS CodePipeline and CodeDeploy for continuous deployment.
- Deploy a Node.js Express app to an AWS EC2 instance.

## Skills Learned

- **Git and GitHub**: Creating a repository, cloning it, and pushing changes.
- **Node.js and Express**: Building a simple Express application.
- **AWS EC2 Management**: Creating and configuring EC2 instances.
- **AWS IAM Roles**: Setting up roles for CodeDeploy.
- **AWS CodePipeline and CodeDeploy**: Setting up a continuous deployment pipeline and deployment group.

## Tools Used

Here’s a list of the tools and services I used in this project:

- **GitHub**: For version control and repository hosting.
- **Node.js and Express**: For building the backend application.
- **AWS EC2**: For hosting the application.
- **AWS CodePipeline**: For automating the deployment workflow.
- **AWS CodeDeploy**: For deploying the application to EC2.

## Step-by-Step Guide

### 1. Set Up GitHub Repository

1. Create a new repository on GitHub named `node.js-express-on-aws-ec2`.
2. Make it public and initialize with a README file.

```bash
# Clone the repository to your local machine
git clone https://github.com/yourusername/node.js-express-on-aws-ec2.git
cd node.js-express-on-aws-ec2
```

### 2. Create Node.js Express Application

1. Initialize a `package.json` file with Express dependency.

```bash
npm init -y
npm install express
```

2. Create `app.js` with a basic Express server:

```js
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('<h1>Express Demo App</h1><h4>Success</h4><p>Version 1.0</p>');
});

app.get('/products', (req, res) => {
  res.json([
    { productId: 101, price: 100 },
    { productId: 102, price: 150 }
  ]);
});

app.listen(port, () => {
  console.log(`App is running on port ${port}`);
});
```

### 3. Configure CodeDeploy App

1. Created `appspec.yml` in the root directory:

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/node-app
hooks:
  BeforeInstall:
    - location: scripts/before_install.sh
      timeout: 300
  ApplicationStart:
    - location: scripts/application_start.sh
      timeout: 300
```

2. Created `scripts` folder and added scripts:

- `before_install.sh`

```bash
#!/bin/bash
# Install NVM and Node.js
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install node
mkdir -p /home/ec2-user/node-app
```

- `application_start.sh`

```bash
#!/bin/bash
cd /home/ec2-user/node-app
npm install
nohup node app.js > app.log 2>&1 &
```

### 4. Set Up AWS EC2 Instance

1. Created an EC2 instance with Amazon Linux 2.
2. Add IAM roles: `ec2-code-deploy-role` and `code-deploy-role`.

```bash
# Configured instance with necessary security groups
aws ec2 create-security-group --group-name my-security-group --description "My security group"
aws ec2 authorize-security-group-ingress --group-name my-security-group --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-name my-security-group --protocol tcp --port 3000 --cidr 0.0.0.0/0
```

### 5. Set Up CodeDeploy Application and Deployment Group

1. Created CodeDeploy application and deployment group:

- Go to AWS CodeDeploy and create an application named `express-app`.
- Create a deployment group with the EC2 instance and roles specified earlier.

### 6. Created AWS CodePipeline

1. Create a pipeline named `express-app-pipeline`:

- Connect GitHub repository.
- Configure Source stage with GitHub.
- Set up Deploy stage using CodeDeploy.

### 7. Deploy Application

1. Commited and pushed changes to GitHub:

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

2. Monitored deployment in AWS CodeDeploy and CodePipeline.

### 8. Verify Deployment

- Accessed the EC2 instance IP at port 3000 to see the running app.

```bash
http://<EC2-PUBLIC-IP>:3000
```

## Conclusion

Node.js application deployed on AWS EC2 with continuous deployment configured using CodePipeline and CodeDeploy.
