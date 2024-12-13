# To-Do App with Docker Compose and AWS Infrastructure

This is a simple To-Do application built using Node.js and MongoDB, with Docker Compose for easy containerization and orchestration. The app is designed to run in an AWS environment, where the **Node.js app** will be hosted on a **public EC2 instance** and the **MongoDB database** will reside in a **private subnet**. This setup ensures that the database is not directly accessible from the public internet while allowing the Node.js app to connect to it securely.

## Features

- **Add To-Dos**: Create new to-dos with a title and description.
- **Delete To-Dos**: Remove existing to-dos.
- **MongoDB**: A persistent database for storing to-do data, hosted in a private subnet.
- **Docker & Docker Compose**: For containerizing the app and managing multi-container environments.

## Technologies Used

- **Node.js**: Backend for the To-Do API.
- **Express.js**: Web framework for Node.js.
- **MongoDB**: NoSQL database for storing to-do data.
- **Docker & Docker Compose**: For containerizing the app and managing multi-container environments.
- **AWS**: Hosting the application in a secure, scalable cloud environment with proper networking configuration.

## AWS Infrastructure Overview

- **Public EC2 Instance**: This instance will host the **Node.js** application and expose it to the public internet.
- **Private MongoDB Instance**: MongoDB will be hosted on a private EC2 instance or an RDS instance in a **private subnet**. The public EC2 instance will connect to this private database instance.
- **VPC**: The app will run within a custom **Virtual Private Cloud (VPC)** with both **public** and **private subnets**.
- **Security Groups**: Proper security groups will be configured to ensure only the **public EC2 instance** can communicate with the **private MongoDB** instance.
- **NAT Gateway**: The private subnet will use a **NAT gateway** in the public subnet to allow outbound internet access for things like updates, API calls, etc.
- **Elastic IP**: The public EC2 instance will be associated with an **Elastic IP** (EIP) to ensure it has a static public IP for inbound access.

## Requirements

- **Docker**
- **Docker Compose**
- **AWS Account**: To configure the infrastructure
- **AWS CLI** (optional, for infrastructure automation)
- **VPC**: With at least one **public** and one **private** subnet.
- **Security Groups**: Set up to control inbound and outbound traffic.

## Getting Started

### 1. Clone the Repository

First, clone the repository to your local machine:

```bash
git clone https://github.com/your-username/todo-app.git
cd todo-app
```

### 2. Set Up Docker and Docker Compose

Make sure you have Docker and Docker Compose installed on your local machine. You can check if Docker is installed by running:

```bash
docker --version
docker-compose --version
```

### 3. Build and Run the Application

Run the following command to build and start the application using Docker Compose:

```bash
docker-compose up --build
```

This command will:

- Build the Docker images for the app and MongoDB.
- Start both the **Node.js** application and **MongoDB** container.
- Map port `3000` of the host to `3000` of the container for the Node.js app.
- Mount the current directory to the container so you can make live code changes.

### 4. Access the Application

Once the containers are running locally, you can access the To-Do app by navigating to `http://localhost:3000` in your browser.

### 5. Deploy to AWS

#### Step 1: **Create a VPC**

Create a new **VPC** with the following components:

- **Public Subnet**: For the EC2 instance that will host the Node.js app.
- **Private Subnet**: For the MongoDB instance that will not be accessible from the public internet.
- **Internet Gateway**: Attach it to the VPC for internet access to the public subnet.
- **NAT Gateway**: Allow the private subnet to access the internet for outgoing connections.

You can use the **AWS Management Console** or **CloudFormation** to set up these resources.

#### Step 2: **Create an EC2 Instance (Node.js App)**

- Launch an **EC2 instance** in the **public subnet** of your VPC.
- Assign a **security group** allowing inbound traffic on port `3000` (or any other port you choose for your app).
- **Install Docker** and **Docker Compose** on the EC2 instance.
- **Clone the repository** to the EC2 instance or upload your code to the instance.
- **Run the application** using the `docker-compose up --build` command.

#### Step 3: **Create a MongoDB Instance (Private Subnet)**

- Launch an **EC2 instance** (or use **Amazon RDS**) in the **private subnet** to host MongoDB.
- Assign a **security group** that only allows inbound connections from the **public EC2 instance**.
- Make sure MongoDB is properly configured and running on this instance.
- **Store the MongoDB connection URI** (i.e., `mongodb://root:example@<private-db-ip>:27017/todo-app`) for connecting from the Node.js app.

#### Step 4: **Configure the Node.js App to Connect to MongoDB**

In the **.env** file, set the MongoDB URI to point to your **private MongoDB instance**:

```
MONGO_URI=mongodb://root:example@<private-db-ip>:27017/todo-app?authSource=admin
PORT=3000
NODE_ENV=production
```

#### Step 5: **Set Up Security Groups**

- For the **public EC2 instance** (Node.js app):

  - Allow inbound connections on port `3000` (or your chosen port).
  - Allow outbound connections to the MongoDB instance in the private subnet.

- For the **private EC2 instance** (MongoDB):
  - Allow inbound connections from the **public EC2 instance** on the MongoDB port (e.g., port `27017`).
  - Deny all inbound traffic from outside the VPC.

#### Step 6: **Elastic IP (EIP) for Public EC2 Instance**

- **Allocate an Elastic IP** (EIP) in your AWS account.
- **Associate the EIP** with your **public EC2 instance** to ensure the instance has a static public IP.

### 6. Stopping the Application

To stop the application and remove the containers, use the following command:

```bash
docker-compose down
```

This will stop the services and remove the containers. If you want to stop the services without removing the containers, use:

```bash
docker-compose stop
```
