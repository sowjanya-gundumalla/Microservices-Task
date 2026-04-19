# Microservices-Task

A microservices-based Node.js application containerized using Docker and Docker Compose.

## Architecture

| Service         | Port | Description                          |
|-----------------|------|--------------------------------------|
| user-service    | 3000 | Handles user data                    |
| product-service | 3001 | Manages product listings             |
| order-service   | 3002 | Manages orders                       |
| gateway-service | 3003 | API Gateway routes to all services   |

All services communicate over a shared Docker bridge network called microservices-net.

## Project Structure

```
Microservices/
├── user-service/
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
├── product-service/
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
├── order-service/
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
├── gateway-service/
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

## Prerequisites

- Docker v20 or above
- Docker Compose v2 or above

Verify installations:

```bash
docker --version
docker compose version
```

## Setup and Running

Clone the repository:

```bash
git clone <your-forked-repo-url>
cd Microservices-Task/Microservices
```

Build and start all services:

```bash
docker-compose up --build
```

Run in background:

```bash
docker-compose up --build -d
```

Stop all services:

```bash
docker-compose down
```

## Testing Each Service

Once running, test each service using your browser or curl:

User Service:
```bash
curl http://localhost:3000
```

Product Service:
```bash
curl http://localhost:3001
```

Order Service:
```bash
curl http://localhost:3002
```

Gateway Service:
```bash
curl http://localhost:3003
```

## Verifying Containers

Check all running containers:
```bash
docker ps
```

View logs of a specific service:
```bash
docker logs user-service
docker logs product-service
docker logs order-service
docker logs gateway-service
```

## Troubleshooting

Port already in use:
```bash
# Linux/Mac
lsof -i :3000
kill -9 <PID>

# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

Container exits immediately:
```bash
docker logs <container-name>
```
Common cause: typo in Dockerfile CMD instruction or missing app.js.

Services cannot communicate:
All services must be on the same microservices-net network in docker-compose.yml. Services reference each other by service name, for example http://user-service:3000, not localhost.

Changes not reflecting after code edit:
```bash
docker-compose up --build
```
The --build flag is required to rebuild images with the latest code changes. Without it Docker reuses the old cached image.

Typo in docker-compose.yml service name:
If depends_on references a service name that does not exactly match the service definition, Docker will throw an invalid compose project error. Check spelling carefully.

## Notes

- Entry point for all services is app.js
- gateway-service uses depends_on to start after all other services
- restart: unless-stopped ensures containers auto-restart on crashes but respect manual stops
- Port mapping format is host:container, for example 3000:3000 means your machine port 3000 maps to container port 3000
- Containers communicate internally using service names as hostnames, not localhost
```
