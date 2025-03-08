
# **buy-01 E-commerce Platform** 🛒

## **Overview** 🌟
A **microservices-based e-commerce platform** built with **Spring Boot** and **Angular**, supporting multiple **user roles** (clients/sellers), **product management**, and **media handling**. The platform features:
- **Secure authentication** 🔐
- **Product CRUD** operations for sellers 🛍️
- **Integrated media management** for images 🖼️

## **Architecture** 🏗️
The platform consists of the following **microservices**:
- **Registry Service**: Service discovery and registration 🔍
- **Gateway Service**: API gateway and routing 🔄
- **User Service**: User management and authentication 👤
- **Product Service**: Product CRUD operations for sellers 🏷️
- **Media Service**: Image upload and management 📸

Communication between services is facilitated via **Kafka** 🔈.

## **Tech Stack** ⚙️
- **Backend**:
    - Spring Boot 🚀
    - Spring Security (JWT/OAuth2) 🔐
    - Apache Kafka 🐄
    - MongoDB 📊
    - Docker 🐳
- **Frontend**:
    - Angular 🅰️
    - TypeScript 💻
    - Angular Material 🎨
    - PrimeNG 🧩

## **Key Features** 🔑
- **Role-based user management** (clients/sellers) 👥
- **Secure authentication** with JWT/OAuth2 🔒
- **Product management** (CRUD) for sellers 🛠️
- **Media upload with validation** (2MB limit) 📁
- **Seller-specific dashboards** 📊
- **Product listing views** for all users 🛍️

## **Prerequisites** 🛠️
Make sure you have the following installed:
- **Java 17 or higher** ☕
- **Maven** 📦
- **Docker** and **Docker Compose** 🐳
- **Node.js** and **npm** 🌱
- **Make** (for using the Makefile) ⚙️

## **Project Structure** 📂
```
buy-01/
├── api/
│   ├── registry/      # Service registry
│   ├── gateways/      # API gateway
│   ├── users/         # User management service
│   ├── products/      # Product management service
│   └── media/         # Media handling service
├── frontend/          # Angular application
├── docker-compose.dep.yml
├── docker-compose.services.yml
└── Makefile
```

## **Setup and Installation** 🏗️

### **HOW_TO_RUN (the whole project)** 🚀
To launch the entire project:
```bash
COMPOSE_PROFILES=run docker-compose up -d
```

### **1. Dependencies** 🔌
Launch the required dependencies:
```bash
make launch-dependencies
```

### **2. Services** 🚀
Build and launch all services:
```bash
make launch-services
```

To stop dependencies:
```bash
make down-dependencies
```

To stop services:
```bash
make down-services
```

### **Individual Service Management** 🛠️
Build specific services:
```bash
make build-registery
make build-gateways
make build-users
make build-products
make build-media
```

## **Security Measures** 🔐
- **HTTPS encryption** using Let's Encrypt SSL certificates 🔒
- **Password hashing and salting** 🔑
- **Role-based access control** (RBAC) 🛡️
- **Secure file upload validation** (e.g., 2MB image limit) 📸
- **Protected sensitive information** (No exposure in API responses) 🔒

## **Error Handling** ⚠️
The platform implements **robust error handling**:
- **File size and type validation** 🛑
- **Role-based access violations** 🚫
- **Form validation** ✅
- **API error responses** with appropriate status codes 📉