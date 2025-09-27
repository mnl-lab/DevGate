# Docker Setup for DevGate

## Prerequisites

- Docker installed on your system
- Docker Compose (usually comes with Docker Desktop)

## Building and Running the Application

### Using Docker Compose (Recommended)

1. Build and start the application:
```bash
docker-compose up --build
```

2. The application will be available at `http://localhost:3000`

3. To run in detached mode:
```bash
docker-compose up -d --build
```

4. To stop the application:
```bash
docker-compose down
```

### Using Docker Commands Directly

1. Build the Docker image:
```bash
docker build -t devgate .
```

2. Run the container:
```bash
docker run -p 3000:80 devgate
```

## Docker Configuration Details

### Multi-Stage Build
The Dockerfile uses a multi-stage build approach:
- **Build Stage**: Uses Node.js 18 Alpine to install dependencies and build the Vue.js application
- **Production Stage**: Uses Nginx Alpine to serve the built static files

### Port Configuration
- Internal port: 80 (nginx default)
- External port: 3000 (configurable in docker-compose.yml)

### Environment Variables
You can add environment variables in the docker-compose.yml file or pass them when running docker commands:

```bash
docker run -p 3000:80 -e NODE_ENV=production devgate
```

### Volume Mounting (Development)
For development with hot reload, you can mount the source code:

```yaml
services:
  devgate:
    # ... other configs
    volumes:
      - .:/app
      - /app/node_modules
```

## Troubleshooting

1. **Port already in use**: Change the external port in docker-compose.yml from 3000 to another port
2. **Build fails**: Make sure all dependencies are properly defined in package.json
3. **Firebase connection issues**: Ensure Firebase configuration is properly set up in your environment

## Production Deployment

For production deployment, consider:
1. Using specific version tags instead of `latest`
2. Setting up proper environment variables for Firebase
3. Implementing health checks
4. Using a reverse proxy (like nginx) in front of the container
5. Setting up SSL/TLS certificates