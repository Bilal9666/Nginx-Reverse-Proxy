
# Nginx Reverse Proxy with Docker - DevOps Internship Assignment

## Project Overview

This project demonstrates how to set up a system using Docker and Nginx Reverse Proxy. The goal is to containerize two backend services and route traffic to them through an Nginx Reverse Proxy using Docker Compose.

All services are containerized and communicate using Docker's default bridge network. The entire system runs with a single command.

## Project Structure

.
├── docker-compose.yml
├── nginx
│   ├── nginx.conf
│   └── Dockerfile
├── service_1
│   ├── Dockerfile
│   └── main.go
├── service_2
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py
└── README.md

## Step-by-Step Setup

### 1. Writing the Backend Services

#### Service 1 - Go Application (service_1/main.go)

A simple Go web server with basic endpoints.

Key Endpoints:
- `/ping` - Returns service status
- `/hello` - Returns a hello message
- `/health` - Used for Docker health checks

#### Service 2 - Python Flask Application (service_2/app.py)

A simple Python Flask server with similar endpoints.

Key Endpoints:
- `/ping` - Returns service status
- `/hello` - Returns a hello message
- `/health` - Used for Docker health checks

### 2. Writing Dockerfiles for Both Services

#### Dockerfile for Service 1 (Go Application)

Located in service_1/

```
FROM golang:1.20

WORKDIR /app

COPY . .

RUN go mod init service1
RUN go mod tidy
RUN go build -o service_1 .

EXPOSE 8001

CMD ["./service_1"]
```

#### Dockerfile for Service 2 (Python Application)

Located in service_2/

```
FROM python:3.10

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 8002

CMD ["python", "app.py"]
```

### 3. Creating requirements.txt for Service 2

Located in service_2/requirements.txt

```
flask
```

### 4. Nginx Reverse Proxy Setup

#### Nginx Configuration File (nginx/nginx.conf)

```
events {}

http {
    log_format custom '$remote_addr - [$time_local] "$request" ';
    access_log /var/log/nginx/access.log custom;

    server {
        listen 80;

        location /service1/ {
            proxy_pass http://service1:8001/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            rewrite ^/service1/(.*)$ /$1 break;
        }

        location /service2/ {
            proxy_pass http://service2:8002/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            rewrite ^/service2/(.*)$ /$1 break;
        }
    }
}
```

#### Dockerfile for Nginx (nginx/Dockerfile)

```
FROM nginx:alpine

COPY nginx.conf /etc/nginx/nginx.conf
```

### 5. Docker Compose Setup

```
services:
  service1:
    build: ./service_1
    container_name: service1
    ports:
      - "8001:8001"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8001/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  service2:
    build: ./service_2
    container_name: service2
    ports:
      - "8002:8002"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8002/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    build: ./nginx
    container_name: nginx
    ports:
      - "8080:80"
    depends_on:
      - service1
      - service2
```

## Running the Project

In the project directory, run:

```
docker-compose up --build
```

## Accessing the Services

- http://localhost:8080/service1/ping
- http://localhost:8080/service1/hello
- http://localhost:8080/service2/ping
- http://localhost:8080/service2/hello

## Health Checks

Both services expose a `/health` endpoint.

Manual check:

```
curl http://localhost:8001/health
curl http://localhost:8002/health
```

## Deployment to AWS

Works the same on an AWS EC2 instance:

1. Launch EC2 instance
2. Install Docker and Docker Compose
3. Clone repository
4. Run `docker-compose up --build`
5. Access services using public IP

Ensure port 8080 is allowed in the security group.

## Conclusion

Demonstrates:
- Containerizing services with Docker
- Nginx reverse proxy routing
- Health checks for reliability
- Local and AWS compatibility
