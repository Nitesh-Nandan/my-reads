# Load Balancer Complete Guide

A comprehensive tutorial covering everything you need to know about load balancers, from basic concepts to advanced implementation strategies.

## Table of Contents
1. [What is a Load Balancer?](#what-is-a-load-balancer)
2. [Types of Load Balancers](#types-of-load-balancers)
3. [Load Balancing Algorithms](#load-balancing-algorithms)
4. [Implementation Examples](#implementation-examples)
5. [Health Checks & Monitoring](#health-checks--monitoring)
6. [Best Practices](#best-practices)
7. [Common Use Cases](#common-use-cases)

## What is a Load Balancer?

A load balancer distributes incoming network traffic across multiple servers to ensure no single server becomes overwhelmed, improving application availability, reliability, and performance.

**Key Benefits:**
- **High Availability**: Traffic continues even if servers fail
- **Scalability**: Easy to add/remove servers
- **Performance**: Better response times and throughput
- **Maintenance**: Zero-downtime deployments

## Types of Load Balancers

### 1. Application Load Balancer (ALB) - Layer 7
**Best for:** Web applications, microservices, complex routing

**Features:**
- HTTP/HTTPS traffic routing
- Path-based routing (`/api/*` → backend, `/static/*` → CDN)
- Host-based routing (subdomain routing)
- SSL termination
- Advanced health checks

**Example Use Case:**
```
/api/users → User Service
/api/orders → Order Service
/static/* → CDN
/admin/* → Admin Panel
```

### 2. Network Load Balancer (NLB) - Layer 4
**Best for:** High-performance, TCP/UDP traffic

**Features:**
- Ultra-low latency
- TCP/UDP protocol support
- Source IP preservation
- Millions of requests per second
- Connection-based load balancing

**Example Use Case:**
```
Database connections
Real-time gaming
IoT device communication
High-frequency trading
```

### 3. Classic Load Balancer (CLB)
**Best for:** Legacy applications only
**Note:** Avoid for new deployments - use ALB or NLB instead

## Load Balancing Algorithms

### Layer 4 (Network) Algorithms

| Algorithm | How It Works | Best For | Example |
|-----------|--------------|-----------|---------|
| **Round Robin** | Sequential distribution | Simple load balancing | Basic web servers |
| **Least Connections** | Fewest active connections | Long-lived connections | Database pools |
| **IP Hash** | Client IP determines server | Session persistence | User sessions |
| **Weighted Round Robin** | Round robin with server weights | Mixed capacity servers | Gradual scaling |

### Layer 7 (Application) Algorithms

| Algorithm | How It Works | Best For | Example |
|-----------|--------------|-----------|---------|
| **Path-Based** | URL path routing | Microservices | `/api/*` → API servers |
| **Header-Based** | HTTP header routing | A/B testing | `User-Agent` routing |
| **Cookie Hash** | Session cookie routing | Sticky sessions | User preferences |
| **Least Response Time** | Fastest server | Performance critical | API optimization |

## Implementation Examples

### Basic Round Robin Setup
```yaml
# Nginx configuration
upstream backend {
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
    server 192.168.1.12:8080;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

### Path-Based Routing (ALB)
```yaml
# AWS Application Load Balancer
Rules:
  - Path: /api/*
    Target: api-servers
  - Path: /static/*
    Target: cdn-servers
  - Path: /admin/*
    Target: admin-servers
```

### Session Persistence
```yaml
# Sticky sessions configuration
upstream backend {
    ip_hash;  # IP-based sticky sessions
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
}
```

## Health Checks & Monitoring

### Essential Health Check Parameters
```yaml
Health Check Configuration:
  Protocol: HTTP/HTTPS
  Path: /health
  Port: 8080
  Interval: 30s
  Timeout: 5s
  Healthy Threshold: 2
  Unhealthy Threshold: 3
```

### Key Metrics to Monitor
- **Response Time**: Target < 200ms
- **Error Rate**: Target < 1%
- **Throughput**: Requests per second
- **Server Health**: Up/down status
- **Connection Count**: Active connections per server

### Health Check Endpoint Example
```python
# Simple health check endpoint
@app.route('/health')
def health_check():
    try:
        # Check database connection
        db.execute("SELECT 1")
        # Check external dependencies
        return {"status": "healthy"}, 200
    except Exception as e:
        return {"status": "unhealthy", "error": str(e)}, 503
```

## Best Practices

### 1. **Health Check Design**
- Use lightweight endpoints (`/health`, `/ping`)
- Check critical dependencies (database, cache)
- Return appropriate HTTP status codes
- Avoid heavy operations in health checks

### 2. **Load Balancer Selection**
```
Web Applications → ALB (path-based routing)
High Performance → NLB (TCP/UDP)
Simple Load Balancing → Round Robin
Complex Routing → ALB with rules
```

### 3. **Session Management**
- Use sticky sessions when needed
- Implement distributed sessions (Redis)
- Consider stateless applications
- Plan for session failover

### 4. **Security Considerations**
- Enable SSL termination
- Use WAF (Web Application Firewall)
- Implement rate limiting
- Monitor for DDoS attacks

## Common Use Cases

### Web Application
```
Client → ALB → Web Servers
         ↓
    /api/* → API Servers
    /static/* → CDN
    /* → Web Servers
```

### Microservices Architecture
```
Client → API Gateway → Service Discovery
         ↓
    /users → User Service
    /orders → Order Service
    /payments → Payment Service
```

### Database Load Balancing
```
Application → NLB → Database Replicas
              ↓
         Read Replicas (Round Robin)
         Write → Primary Database
```

### High Availability Setup
```
Internet → Load Balancer → Auto Scaling Group
         ↓
    Health Checks → Remove Unhealthy Instances
    Auto Scaling → Add/Remove Instances
```

## Quick Decision Matrix

| Requirement | Recommended Solution |
|-------------|---------------------|
| Simple web app | ALB + Round Robin |
| High performance | NLB + Least Connections |
| Microservices | ALB + Path-based routing |
| Session persistence | ALB + Sticky sessions |
| Database connections | NLB + IP Hash |
| A/B testing | ALB + Header-based routing |

## Troubleshooting Common Issues

### 1. **Servers Not Receiving Traffic**
- Check health check configuration
- Verify security groups/firewall rules
- Ensure target groups are properly configured

### 2. **Uneven Traffic Distribution**
- Review load balancing algorithm
- Check server weights
- Monitor server health status

### 3. **Session Loss**
- Verify sticky session configuration
- Check cookie settings
- Ensure consistent routing rules

### 4. **High Latency**
- Monitor server response times
- Check network configuration
- Review health check intervals

## Summary

Load balancers are essential for building scalable, high-availability applications. Choose the right type and algorithm based on your specific requirements:

- **ALB**: Complex routing, HTTP/HTTPS, microservices
- **NLB**: High performance, TCP/UDP, low latency
- **Algorithms**: Match to your traffic patterns and requirements

Start simple and evolve your load balancing strategy as your application grows. Focus on health checks, monitoring, and choosing the right tool for the job.