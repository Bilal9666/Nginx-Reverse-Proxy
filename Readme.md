
# Nginx Reverse Proxy with Docker - DevOps Internship Assignment

## Project Overview

This project demonstrates how to set up a system using Docker and Nginx Reverse Proxy. The goal is to containerize two backend services and route traffic to them through an Nginx Reverse Proxy using Docker Compose.

All services are containerized and communicate using Docker's default bridge network. The entire system runs with a single command.

## Project Structure

The project contains:

- `docker-compose.yml` to define and run multi-container applications
- An `nginx` directory containing the Nginx configuration and Dockerfile
- A `service_1` directory containing the Go application and its Dockerfile
- A `service_2` directory containing the Python Flask application, its requirements file, and Dockerfile
- A README file to document the project

## Step-by-Step Setup

### 1. Writing the Backend Services

Service 1 is a simple Go web server with basic endpoints to check its status and functionality.

Service 2 is a Python Flask server with similar endpoints for status and functionality.

### 2. Writing Dockerfiles for Both Services

Each service has its own Dockerfile that sets up the necessary environment, copies the source code, installs dependencies, and exposes the relevant ports.

### 3. Creating requirements.txt for Service 2

This file specifies the Python dependencies required to run the Flask application.

### 4. Nginx Reverse Proxy Setup

The Nginx configuration routes incoming traffic to either service based on the URL path. It also logs incoming requests for visibility.

A separate Dockerfile is created to build the Nginx container with the custom configuration.

### 5. Docker Compose Setup

Docker Compose orchestrates the three containers:
- Service 1
- Service 2
- Nginx reverse proxy

It defines how the services are built, connected, and how health checks are performed.

### 6.How to Run This Project
Make sure Docker and Docker Compose are installed.

Clone the repository:

git clone https://github.com/Bilal9666/Nginx-Reverse-Proxy.git

Build and run the system using Docker Compose:

docker-compose up --build

## Accessing the Services

Once running, the services are accessible through the Nginx reverse proxy using the following URL paths:

Service 1:
http://localhost:8080/service1/ping
http://localhost:8080/service1/hello

Service 2:
http://localhost:8080/service2/ping
http://localhost:8080/service2/hello

If you deploy the system on an AWS EC2 instance, replace localhost with your EC2 public IP address.

## Health Checks

Both services expose a `/health` endpoint to allow Docker to monitor if they are running properly.

You can also manually test the health checks using:

curl http://localhost:8080/service1/health
curl http://localhost:8080/service2/health

## Deployment to AWS

The same setup works on an AWS EC2 instance:

- Launch an EC2 instance
- Install Docker and Docker Compose
- Clone the project repository
- Run the system using Docker Compose
- Access the services through the public IP and port

Make sure to allow port 8080 in your security group settings.

## Conclusion

This project demonstrates containerization of backend services, reverse proxy routing using Nginx, and reliable service health monitoring. The setup works both locally and on cloud platforms like AWS.
