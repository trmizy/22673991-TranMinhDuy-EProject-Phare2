# E-Commerce Microservices Platform

A scalable microservices-based e-commerce platform built with Node.js, Express, MongoDB, and RabbitMQ, featuring authentication, product management, and order processing capabilities.

## 🏗️ Architecture

This project implements a microservices architecture with the following services:

- **API Gateway** (Port 3003): Single entry point for all client requests
- **Auth Service** (Port 3000): User authentication and authorization
- **Product Service** (Port 3001): Product catalog management and order creation
- **Order Service** (Port 3002): Order processing and management
- **MongoDB**: Database for all services
- **RabbitMQ**: Message broker for inter-service communication

## 📋 Prerequisites

- **Docker** and **Docker Compose** (v3.8+)
- **Node.js** (v18+) - for local development
- **MongoDB** (v6.0+) - if running locally
- **RabbitMQ** (v3.11+) - if running locally

## 🚀 Quick Start

### Using Docker Compose (Recommended)

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Docker_Test_V4-final
   ```

2. **Set up environment variables**
   
   Create `.env` files in each service directory:

   **auth/.env**
   ```env
   PORT=3000
   JWT_SECRET=your_jwt_secret_key_here
   MONGODB_AUTH_URI=mongodb://mongodb:27017/auth
   ```

   **product/.env**
   ```env
   PORT=3001
   JWT_SECRET=your_jwt_secret_key_here
   MONGODB_PRODUCT_URI=mongodb://mongodb:27017/product
   RABBITMQ_URI=amqp://admin:admin@rabbitmq:5672
   RABBITMQ_QUEUE_PRODUCT=products
   RABBITMQ_QUEUE_ORDER=orders
   ORDER_SERVICE_URL=http://order-service:3002
   ```

   **order/.env**
   ```env
   PORT=3002
   JWT_SECRET=your_jwt_secret_key_here
   MONGODB_ORDER_URI=mongodb://mongodb:27017/order
   RABBITMQ_URI=amqp://admin:admin@rabbitmq:5672
   RABBITMQ_QUEUE_ORDER=orders
   RABBITMQ_QUEUE_PRODUCT=products
   ```

3. **Start all services**
   ```bash
   docker-compose up -d
   ```

4. **Verify services are running**
   ```bash
   docker-compose ps
   ```

### Local Development

1. **Install dependencies for each service**
   ```bash
   cd auth && npm install
   cd ../product && npm install
   cd ../order && npm install
   cd ../api-gateway && npm install
   ```

2. **Start MongoDB and RabbitMQ locally** or update `.env` files with connection strings

3. **Start each service in separate terminals**
   ```bash
   # Terminal 1 - Auth Service
   cd auth && npm start

   # Terminal 2 - Product Service
   cd product && npm start

   # Terminal 3 - Order Service
   cd order && npm start

   # Terminal 4 - API Gateway
   cd api-gateway && npm start
   ```

## 📡 API Endpoints

### Authentication Service (via API Gateway: `/auth`)

#### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "username": "user123",
  "password": "securepassword"
}
```

#### Login
```http
POST /auth/login
Content-Type: application/json

{
  "username": "user123",
  "password": "securepassword"
}

Response: { "token": "jwt_token_here" }
```

#### Access Dashboard
```http
GET /auth/dashboard
Authorization: Bearer <jwt_token>
```

### Product Service (via API Gateway: `/products`)

#### Create Product
```http
POST /products
Authorization: Bearer <jwt_token>
Content-Type: application/json

{
  "name": "Product Name",
  "description": "Product Description",
  "price": 29.99
}
```

#### Get All Products
```http
GET /products
Authorization: Bearer <jwt_token>
```

#### Get Product by ID
```http
GET /products/:id
Authorization: Bearer <jwt_token>
```

#### Create Order (Buy Products)
```http
POST /products/buy
Authorization: Bearer <jwt_token>
Content-Type: application/json

[
  {
    "_id": "product_id_1",
    "quantity": 2
  },
  {
    "_id": "product_id_2",
    "quantity": 1
  }
]
```

### Order Service (via API Gateway: `/orders`)

#### Get All Orders
```http
GET /orders
Authorization: Bearer <jwt_token>
```

#### Get Order by ID
```http
GET /orders/:id
Authorization: Bearer <jwt_token>
```

## 🧪 Testing

### Run All Tests
```bash
npm test
```

### Run Tests for Specific Services

**Auth Service**
```bash
cd auth && npm test
```

**Product Service**
```bash
cd product && npm test
```

### CI/CD Pipeline

The project includes GitHub Actions workflow for automated testing and Docker image building:

- **Triggers**: Push and Pull Requests
- **Jobs**: 
  - `test-auth`: Runs auth service tests
  - `test-product`: Runs product service tests
  - `build-and-push`: Builds and pushes Docker images to Docker Hub

**Required GitHub Secrets**:
- `JWT_SECRET`: JWT secret key
- `DOCKER_USERNAME`: Docker Hub username
- `DOCKER_PASSWORD`: Docker Hub password
- `LOGIN_TEST_USER`: Test user username
- `LOGIN_TEST_PASSWORD`: Test user password

## 🔧 Project Structure

```
.
├── api-gateway/          # API Gateway service
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
├── auth/                 # Authentication service
│   ├── src/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── middlewares/
│   │   ├── config/
│   │   ├── test/
│   │   └── app.js
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
├── product/              # Product service
│   ├── src/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── utils/
│   │   ├── test/
│   │   ├── config.js
│   │   └── app.js
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
├── order/                # Order service
│   ├── src/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── utils/
│   │   ├── config.js
│   │   └── app.js
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
├── .github/
│   └── workflows/
│       └── test.yml      # CI/CD pipeline
├── docker-compose.yml    # Docker Compose configuration
├── package.json          # Root package.json
└── README.md
```

## 🛠️ Technologies

- **Backend Framework**: Express.js
- **Database**: MongoDB with Mongoose ODM
- **Message Broker**: RabbitMQ with amqplib
- **Authentication**: JWT (jsonwebtoken) + bcryptjs
- **API Gateway**: http-proxy
- **Testing**: Mocha + Chai + Chai-HTTP
- **Containerization**: Docker + Docker Compose
- **CI/CD**: GitHub Actions

## 🔐 Security

- Passwords are hashed using bcryptjs with salt rounds
- JWT tokens for stateless authentication
- Environment variables for sensitive configuration
- Authorization middleware on protected routes

## 📊 Service Communication

1. **Synchronous**: API Gateway proxies HTTP requests to services
2. **Asynchronous**: Product and Order services communicate via RabbitMQ queues
   - Product Service publishes to `orders` queue
   - Order Service consumes from `orders` queue and publishes to `products` queue

## 🐛 Troubleshooting

### Services won't start
- Check if ports 3000-3003, 5672, 15672, 27017 are available
- Verify MongoDB and RabbitMQ are running: `docker-compose ps`

### RabbitMQ connection errors
- Wait for RabbitMQ health check to pass (configured with retries)
- Access RabbitMQ Management UI: http://localhost:15672 (admin/admin)

### MongoDB connection timeout
- Increase `serverSelectionTimeoutMS` in connection options
- Verify MongoDB container is healthy: `docker logs mongodb`

### Test failures
- Ensure test environment variables are set correctly
- Make sure MongoDB is running and accessible
- For product tests, auth service must be available

## 📝 License

ISC

## 👥 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📞 Support

For issues and questions, please open an issue in the GitHub repository.