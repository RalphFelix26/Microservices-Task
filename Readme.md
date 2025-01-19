MICROSERVICES DEPLOYMENT: SKILL TEST

This provides instructions for creating Dockerfiles, configuring Docker Compose, and deploying a multi-service application on an EC2 instance.

 1. Dockerfile Creation

User-service

  FROM node:18-alpine
  WORKDIR /app
  COPY package*.json ./  
  RUN npm install  
  COPY . .  
  EXPOSE 3000  
  CMD ["node", "app.js"]

Product-service

  FROM node:18-alpine  
  WORKDIR /app  
  COPY package*.json ./  
  RUN npm install  
  COPY . .  
  EXPOSE 3001
  CMD ["node", "app.js"]

Order-service

  FROM node:18-alpine  
  WORKDIR /app  
  COPY package*.json ./  
  RUN npm install  
  COPY . .  
  EXPOSE 3002  
  CMD ["node", "app.js"]

Gateway-service

  FROM node:18-alpine  
  WORKDIR /app  
  COPY package*.json ./  
  RUN npm install  
  COPY . .  
  EXPOSE 3003  
  CMD ["node", "app.js"]


Notes
1.	FROM node:18-alpine: Uses the official Node.js 18 image with Alpine Linux for a smaller image size.
2.	WORKDIR /app: Sets the working directory within the container to /app.
3.	COPY package*.json ./: Copies package.json and package-lock.json to the working directory.
4.	RUN npm install: Installs the project dependencies.
5.	COPY . .: Copies the entire project directory into the container.
6.	EXPOSE <port>: Exposes the port for each service (3000, 3001, 3002, 3003).
7.	CMD ["node", "app.js"]: Specifies the command to start the service, defined in package.json.
________________________________________
2. Docker Compose Configuration

Create a docker-compose.yml file with the following content:

#version: '3.8'

services:
  user-service:
    build: ./user-service
    ports:
      - "3000:3000"

  product-service:
    build: ./product-service
    ports:
      - "3001:3001"

  order-service:
    build: ./order-service
    ports:
      - "3002:3002"
    depends_on:
      - user-service
      - product-service

  gateway-service:
    build: ./gateway-service
    ports:
      - "3003:3003"
    depends_on:
      - user-service
      - product-service
      - order-service
    networks:
      - microservices-network

networks:
  microservices-network:
    driver: bridge
________________________________________
3. Deploy Application on EC2 Instance
Launch an EC2 Instance
1.	Launch an Ubuntu EC2 instance.
2.	SSH into the instance.
Install Docker
Run the following commands to install Docker:
bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker

Install Docker Compose
bash

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version

Steps to Deploy
1.	Clone the repository containing the Dockerfile and docker-compose.yml.

2.	Navigate to the project directory:
bash
cd /path/to/your/project

3.	Build and run the services:
bash
sudo docker	--build
sudo docker-compose up -d

4. Services and Endpoints
User Service
open in your browser: http://localhost:3000/users or http://<instance_ip>:3000/users

Product Service
in your browser: http://localhost:3001/products or http://<instance_ip>:3001/products

Order Service
open in your browser: http://localhost:3002/orders or http://<instance_ip>:3002/orders

