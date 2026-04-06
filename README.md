# 🍅 Tomato - Microservices Food Delivery Platform

A distributed, production-grade food delivery system built using microservices architecture, featuring real-time order tracking, event-driven communication, and scalable cloud deployment.

---

## 🚀 Features

- Microservices architecture with 6 independent services (auth, admin, restaurant, rider, realtime, utils)
- API Gateway service for unified entry point and request routing
- Event-driven communication using RabbitMQ for reliable inter-service workflows
- Real-time order updates using WebSockets
- Live rider-to-user location tracking (map-based delivery updates)
- Secure authentication with OAuth 2.0
- Custom domain with SSL for secure production access
- Independent CI/CD pipeline per service via GitHub Actions

---

## 🧠 Architecture Overview

This system follows a distributed microservices architecture where each service is independently deployable and communicates via a mix of synchronous and asynchronous patterns.

### Core Services

- **Gateway Service** → Single entry point — routes all client requests to appropriate services
- **Auth Service** → Handles authentication (users, restaurants, riders)
- **Admin Service** → Verifies restaurants and riders before activation
- **Restaurant Service** → Manages menus, orders, and cart operations
- **Rider Service** → Handles delivery agents and assignment
- **Realtime Service** → WebSocket server for live updates and notifications
- **Utils Service** → Payment processing (Stripe, Razorpay) and media (Cloudinary)

---

### Communication Patterns

- **REST APIs** → Synchronous request-response via API Gateway
- **RabbitMQ (CloudAMQP)** → Asynchronous, event-driven workflows between services
- **WebSockets** → Real-time updates (order status, rider tracking)

---

## 🔄 Order Flow (Simplified)

1. User places an order via Gateway → Restaurant Service
2. Order service publishes event to RabbitMQ
3. Restaurant receives order and updates stats
4. Realtime service pushes updates via WebSockets
5. Nearby riders receive notification (within ~500m range)
6. Rider accepts → order status updates in real-time
7. Live location tracking between rider and user until delivery

---

## ⚙️ Tech Stack

### Backend

- Node.js, TypeScript, Express.js
- http-proxy-middleware (API Gateway)

### Frontend

- React, TypeScript, Vite

### Messaging & Realtime

- RabbitMQ (CloudAMQP — managed free tier)
- WebSockets (Socket.IO)

### Database

- MongoDB (Atlas — managed free tier)

### Cloud & DevOps

- Docker (containerization)
- Render (deployment)
- GitHub Actions (CI/CD)
- Docker Hub (container registry)

### External Services

- Stripe, Razorpay (Payments)
- Cloudinary (Media handling)

---

## 🏗️ Deployment Architecture

```
                    ┌─────────────────────────────┐
                    │      www.tomatoapp.store      │
                    │      (React — Vercel)         │
                    └──────────────┬───────────────┘
                                   │ HTTPS
                    ┌──────────────▼───────────────┐
                    │     api.tomatoapp.store      │
                    │       (API Gateway)          │
                    └──┬──────┬──────┬──────┬──────┘
                       │      │      │      │
           ┌───────────▼─┐ ┌──▼───┐ ┌▼────┐ ┌▼──────────────┐
           │ Auth Service│ │Admin │ │Rest │ │Rider + Utils  │
           │  (Render)   │ │      │ │aurant│ │   (Render)    │
           └─────────────┘ └──────┘ └─────┘ └───────────────┘
                    │                              │
           ┌────────▼──────────┐      ┌───────────▼──────┐
           │   MongoDB Atlas   │      │  CloudAMQP        │
           │   (Database)      │      │  (RabbitMQ)       │
           └───────────────────┘      └──────────────────┘

           WebSocket (direct):
           Frontend → api.tomatoapp.store
```

---

## 🔀 API Gateway

All client requests go through a single **Express-based API Gateway** which proxies requests to the appropriate service:

```
/api/auth/*        → Auth Service
/api/v1/admin/*    → Admin Service
/api/restaurant/*  → Restaurant Service
/api/rider/*       → Rider Service
/api/utils/*       → Utils Service
/api/v1/internal/* → Realtime Service
/socket.io/*       → Realtime Service (WebSocket)
```

This mimics the behavior of a cloud load balancer (like AWS ALB) at the application level.

---

## 💰 Cost Optimization

This project was initially deployed on **AWS ECS Fargate** with an **Application Load Balancer (ALB)**. While this provided a production-grade setup, the monthly cost was significant for a portfolio project.

### Migration: AWS → Render

| Component          | AWS         | Render                 |
| ------------------ | ----------- | ---------------------- |
| Compute            | ECS Fargate | Render free/starter    |
| Load Balancer      | ALB         | Express Gateway (free) |
| Container Registry | ECR         | Docker Hub (free)      |
| Total              | ~$130/month | ~$5/month              |

### Trade-offs accepted for cost optimization

All services are **publicly accessible** on Render (each has its own public URL). In a production environment this is not ideal — services should be private and only accessible internally.

#### Production best practice (not implemented due to cost):

```
✅ Services inside a private VPC
✅ Inter-service communication via internal DNS
   e.g. auth-service.internal:5000
✅ Only Gateway publicly accessible
✅ Individual services unreachable from public internet
```

#### Current setup (cost-optimized for portfolio):

```
⚠️  All services have public Render URLs
⚠️  Inter-service communication via public URLs
⚠️  No VPC / private networking
```

This is a deliberate trade-off — acceptable for a portfolio project, not for production.

---

## 📦 Repository Structure

```bash
tomato/
├── frontend/                  # React frontend
├── backend/
│   ├── gateway/             # API Gateway (Express proxy)
│   ├── auth/
│   ├── admin/
│   ├── restaurant/
│   ├── rider/
│   ├── realtime/
│   └── utils/
└── README.md
```

---

## 🔄 CI/CD Pipeline

Each service has its own independent GitHub Actions workflow:

```
Code push (to service folder)
      ↓
GitHub Actions triggers
      ↓
Build Docker image
      ↓
Push to Docker Hub
      ↓
Render auto-deploys new image ✅
```

This ensures independent deployability — changing auth service only redeploys auth, other services remain unaffected.

---

## 🛠️ Setup (Local Development)

1. Clone the repository
2. Install dependencies for each service:

```bash
cd services/auth && npm install
cd services/restaurant && npm install
# repeat for each service
```

3. Configure `.env` for each service
4. Start services:

```bash
cd services/auth && npm run dev
```

---

## 🔗 Live Demo

- **Frontend:** https://www.tomatoapp.store
- **API Gateway:** https://api.tomatoapp.store

---

## 💡 Key Learnings

- Designing scalable microservices with clear service boundaries
- Building an API Gateway from scratch using Express proxy middleware
- Implementing event-driven architecture using message queues
- Handling real-time updates using WebSockets
- Managing inter-service communication (sync vs async trade-offs)
- Understanding VPC, private networking, and why services should not be publicly exposed
- Cost optimization — migrating from AWS ECS ($130/month) to Render ($5/month)
- Containerizing applications with multi-stage Docker builds
- Independent CI/CD pipelines per microservice

---

## 🚀 Future Improvements

- Move to private networking (VPC / internal DNS) for service-to-service communication
- Centralized logging & monitoring (ELK / Prometheus / Grafana)
- Advanced retry & dead-letter queue handling in RabbitMQ
- Improved rider assignment using geo-optimization
- Rate limiting at the API Gateway level
- Service discovery using Consul or AWS Cloud Map

---

## 📌 Notes

This project was built to explore real-world system design concepts such as distributed systems, event-driven architecture, real-time communication, and cloud cost optimization in a production-like environment.

The architecture intentionally mirrors production patterns (API Gateway, message queues, WebSockets, containerization, CI/CD) while making practical trade-offs for a portfolio context (public services, managed free-tier databases).
