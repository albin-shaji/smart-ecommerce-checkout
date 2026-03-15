# ?? Smart E-Commerce Checkout Workflow

A backend microservices system that simulates a real-world e-commerce checkout pipeline - similar to how Amazon or Flipkart handles cart, discount, payment, and inventory behind the scenes.

Built as part of the **Cloud Computing** subject assignment for **MCA**.

---

## ?? Overview

This system connects multiple independent microservices into a complete checkout flow - without any UI. Everything is demonstrated using API calls via Postman.

**Checkout Flow:**
```
Cart  Discount  Payment  Inventory Update  RabbitMQ Event
```

---

## ?? Architecture
```
CLIENT (Postman)
     |
     CDD Port 5001  Inventory Service (Flask)
     CDD Port 5002  Cart Service (Flask)
     CDD Port 5003  Discount Service (Flask)
     @DD Port 5004  Payment Service (Flask)
                          |
               ZDDDDDDDDDDADDDDDDDDDD?
          MySQL (3306)        RabbitMQ (5672)
```

---

## ?? Tech Stack

| Technology | Purpose |
|------------|---------|
| Python + Flask | Microservice REST APIs |
| MySQL 8.0 | Persistent database |
| RabbitMQ 3 | Async message broker |
| Docker | Containerization |
| Postman | API testing & demo |

---

## ?? Project Structure
```
ecommerce/
CDD db-init/
3   @DD init.sql
CDD inventory-service/
3   CDD app.py
3   CDD requirements.txt
3   @DD Dockerfile
CDD cart-service/
3   CDD app.py
3   CDD requirements.txt
3   @DD Dockerfile
CDD discount-service/
3   CDD app.py
3   CDD requirements.txt
3   @DD Dockerfile
CDD payment-service/
3   CDD app.py
3   CDD requirements.txt
3   @DD Dockerfile
CDD README.md
@DD LICENSE
```

---

## ?? Getting Started

### Prerequisites
- Docker Desktop installed and running
- Postman

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/smart-ecommerce-checkout.git
cd smart-ecommerce-checkout
```

### 2. Create Docker Network
```bash
docker network create ecommerce-net
```

### 3. Start Infrastructure
```bash
# MySQL
docker run -d --name mysql-db --network ecommerce-net \
  -e MYSQL_ROOT_PASSWORD=root123 \
  -e MYSQL_DATABASE=ecommerce \
  -p 3306:3306 mysql:8.0

# RabbitMQ
docker run -d --name rabbitmq --network ecommerce-net \
  -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

### 4. Initialize Database
```bash
docker exec -i mysql-db mysql -uroot -proot123 < db-init/init.sql
```

### 5. Build & Run All Services
```bash
# Inventory Service
docker build -t inventory-service ./inventory-service
docker run -d --name inventory --network ecommerce-net -p 5001:5001 inventory-service

# Cart Service
docker build -t cart-service ./cart-service
docker run -d --name cart --network ecommerce-net -p 5002:5002 cart-service

# Discount Service
docker build -t discount-service ./discount-service
docker run -d --name discount --network ecommerce-net -p 5003:5003 discount-service

# Payment Service
docker build -t payment-service ./payment-service
docker run -d --name payment --network ecommerce-net -p 5004:5004 payment-service
```

### 6. Verify All Containers Running
```bash
docker ps
```

---

## ?? Testing the Checkout Flow (Postman)

### Step 1 - Add to Cart
```
POST http://localhost:5002/cart
Body: { "product_id": 1, "quantity": 2 }
```

### Step 2 - Apply Discount
```
POST http://localhost:5003/discount
Body: { "code": "NEWYEAR", "original_price": 100000 }
```

### Step 3 - Process Payment
```
POST http://localhost:5004/payment
Body: { "product_id": 1, "quantity": 2, "discount_code": "NEWYEAR" }
```

### Step 4 - Verify Inventory Updated
```
GET http://localhost:5001/inventory/1
```

### Step 5 - Check RabbitMQ
```
Open http://localhost:15672 (guest/guest)  Queues  payment_processed
```

---

## ??? Discount Codes

| Code | Discount |
|------|----------|
| NEWYEAR | 10% off |
| SAVE20 | 20% off |
| FLAT50 | 50% off |

---

## ??? Service Ports

| Service | Port |
|---------|------|
| Inventory Service | 5001 |
| Cart Service | 5002 |
| Discount Service | 5003 |
| Payment Service | 5004 |
| MySQL | 3306 |
| RabbitMQ | 5672 / 15672 |

---

## ?? License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## ????? Author

**Albin Shaji**
MCA Student | Palakkad, Kerala
