# 🍅 Tomato - Microservices Food Delivery Platform

A distributed, production-grade food delivery system built using microservices architecture, featuring real-time order tracking, event-driven communication, and scalable cloud deployment.

---

## 🚀 Features

- Microservices architecture with 6 independent services (auth, admin, restaurant, rider, realtime, utils).
- Event-driven communication using RabbitMQ for reliable inter-service workflows
- Real-time order updates using WebSockets.
- Live rider-to-user location tracking (map-based delivery updates)
- Secure authentication with OAuth 2.0.
- Scalable deployment on AWS ECS with load balancing (ALB).
- Custom domain with SSL for secure production access.

---

## 🧠 Architecture Overview

This system follows a distributed microservices architecture where each service is independently deployable and communicates via a mix of synchronous and asynchronous patterns.

### Core Services

- **Auth Service** → Handles authentication (users, restaurants, riders).
- **Admin Service** → Verifies restaurants and riders before activation.
- **Restaurant Service** → Manages menus, orders, and cart operations.
- **Rider Service** → Handles delivery agents and assignment.
- **Realtime Service** → WebSocket server for live updates and notifications.
- **Utils Service** → Payment processing (Stripe, Razorpay) and media (Cloudinary).

---

### Communication Patterns

- **REST APIs** → For synchronous request-response operations.
- **RabbitMQ** → For asynchronous, event-driven workflows between services.
- **WebSockets** → For real-time updates (order status, rider tracking).

---

## 🔄 Order Flow (Simplified)

1. User places an order.
2. Order service publishes event to RabbitMQ.
3. Restaurant receives order and updates stats.
4. Realtime service pushes updates via WebSockets.
5. Nearby riders receive notification (within ~500m range).
6. Rider accepts → order status updates in real-time.
7. Live location tracking between rider and user until delivery.

---

## ⚙️ Tech Stack

### Backend

- Node.js, TypeScript
- Express.js

### Frontend

- React

### Messaging & Realtime

- RabbitMQ
- WebSockets

### Database

- MongoDB

### Cloud & DevOps

- Docker
- AWS ECS, ECR, ALB
- GitHub Actions (CI/CD)

### External Services

- Stripe, Razorpay (Payments)
- Cloudinary (Media handling)

---

## 📦 Repository Structure

```bash
tomato/
├── client/
├── services/
│   ├── auth/
│   ├── admin/
│   ├── restaurant/
│   ├── rider/
│   ├── realtime/
│   └── utils/
└── README.md
```

---

## 🛠️ Setup (Local Development)

1. Clone the repository.
2. Install dependencies for each service.
3. Configure environment variables (`.env`).

---

## 🔗 Live Demo

- Frontend: https://www.tomatoapp.store
- Backend APIs: http://tomato-alb-955741339.ap-south-1.elb.amazonaws.com

---

## 💡 Key Learnings

- Designing scalable microservices with clear service boundaries
- Implementing event-driven architecture using message queues
- Handling real-time updates using WebSockets
- Managing inter-service communication (sync vs async trade-offs)
- Deploying containerized applications on AWS with CI/CD

---

## 🚀 Future Improvements

- Centralized logging & monitoring (ELK / Prometheus)
- Advanced retry & dead-letter queue handling
- Improved rider assignment using geo-optimization
- Rate limiting and API gateway integration

---

## 📌 Notes

This project was built to explore real-world system design concepts such as distributed systems, event-driven architecture, and real-time communication in a production-like environment.
