# Complete System Architecture & Running Guide

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    MULTI-VENDOR E-COMMERCE PLATFORM         │
└─────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                         CLIENT APPLICATIONS                      │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐ │
│  │  Customer Portal │  │  Admin Dashboard │  │ Vendor Panel │ │
│  │  (React/Next.js) │  │  (React/Next.js) │  │(React/Next.js)│ │
│  │   Port: 3001     │  │   Port: 3002     │  │ Port: 3003   │ │
│  └──────────────────┘  └──────────────────┘  └──────────────┘ │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
                              ↓ HTTP/HTTPS
┌──────────────────────────────────────────────────────────────────┐
│                        API GATEWAY LAYER                         │
├──────────────────────────────────────────────────────────────────┤
│                    NestJS Backend (Port: 3000)                   │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                      Routes                             │   │
│  │  /api/auth        /api/products   /api/orders           │   │
│  │  /api/vendors     /api/payments   /api/categories       │   │
│  │  /api/users       /api/admin      /api/analytics        │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
          ↓                    ↓                    ↓
┌─────────────────────┐ ┌────────────────┐ ┌────────────────┐
│   PostgreSQL DB     │ │  Redis Cache   │ │  File Storage  │
│   Port: 5432        │ │  Port: 6379    │ │  (AWS S3/Local)│
│                     │ │                │ │                │
│ • Users             │ │ • Sessions     │ │ • Images       │
│ • Products          │ │ • Cart Data    │ │ • Documents    │
│ • Orders            │ │ • Cache        │ │ • Videos       │
│ • Vendors           │ │                │ │                │
│ • Payments          │ └────────────────┘ └────────────────┘
│ • Categories        │
│ • Reviews           │
└─────────────────────┘
```

---

## Step-by-Step System Running

### STEP 1: Prerequisites Installation

#### Install Docker & Docker Compose

**macOS:**
```bash
# Install Docker Desktop (includes Docker & Docker Compose)
# https://www.docker.com/products/docker-desktop

# Verify installation
docker --version
docker-compose --version
```

**Windows:**
```bash
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
# Run installer and complete setup
# Restart system if required

# Verify installation (in PowerShell/CMD)
docker --version
docker-compose --version
```

**Linux (Ubuntu/Debian):**
```bash
# Install Docker
sudo apt-get update
sudo apt-get install docker.io docker-compose

# Add user to docker group (avoid sudo)
sudo usermod -aG docker $USER
newgrp docker

# Verify installation
docker --version
docker-compose --version
```

---

### STEP 2: Clone & Prepare Repository

```bash
# Clone repository
git clone https://github.com/softwaredevelopertaksh/sachin180121.git
cd sachin180121

# Checkout feature branch (if not already on it)
git checkout feature/ecommerce-platform

# Verify branch
git branch
```

---

### STEP 3: Configure Environment Variables

Create `.env` file in root directory:

```bash
cat > .env << 'EOF'
# Application
NODE_ENV=development
ENVIRONMENT=local

# Backend
BACKEND_PORT=3000
BACKEND_URL=http://localhost:3000

# Frontend
FRONTEND_PORT=3001
FRONTEND_URL=http://localhost:3001

# Admin Panel
ADMIN_PORT=3002
ADMIN_URL=http://localhost:3002

# Vendor Panel
VENDOR_PORT=3003
VENDOR_URL=http://localhost:3003

# Database
DB_HOST=postgres
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=ecommerce

# Redis
REDIS_HOST=redis
REDIS_PORT=6379

# JWT
JWT_SECRET=your_super_secret_jwt_key_change_in_production

# Payment Gateway (Optional - for testing)
RAZORPAY_KEY_ID=rzp_test_xxxxxxxxxxxx
RAZORPAY_KEY_SECRET=xxxxxxxxxxxx
STRIPE_SECRET_KEY=sk_test_xxxxxxxxxxxx

# AWS (Optional - for file uploads)
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_REGION=us-east-1
AWS_S3_BUCKET=your_bucket_name
EOF

cat .env
```

---

### STEP 4: Start All Services with Docker Compose

```bash
# Build all images (first time only)
docker-compose build

# Start all services in background
docker-compose up -d

# Check if all services are running
docker-compose ps
```

**Expected Output:**
```
NAME                   COMMAND                  SERVICE      STATUS       PORTS
ecommerce_backend      "npm run dev"           backend      Up 2 mins    0.0.0.0:3000->3000/tcp
ecommerce_frontend     "npm run dev"           frontend     Up 1 min     0.0.0.0:3001->3001/tcp
ecommerce_admin        "npm run dev"           admin-panel  Up 1 min     0.0.0.0:3002->3002/tcp
ecommerce_vendor       "npm run dev"           vendor-panel Up 1 min     0.0.0.0:3003->3003/tcp
ecommerce_db           "docker-entrypoint.s…"  postgres     Up 3 mins    0.0.0.0:5432->5432/tcp
ecommerce_redis        "redis-server"          redis        Up 3 mins    0.0.0.0:6379->6379/tcp
```

---

### STEP 5: View Logs

```bash
# View all logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f backend
docker-compose logs -f frontend
docker-compose logs -f postgres

# Follow last 50 lines
docker-compose logs -f --tail=50 backend
```

---

### STEP 6: Verify All Services Are Running

#### Backend API (Port 3000)

```bash
# Health Check
curl http://localhost:3000/health

# Expected Response:
# {"status":"ok","message":"Backend is running"}
```

#### Frontend (Port 3001)

```bash
# Open in browser
open http://localhost:3001
# OR
curl http://localhost:3001
```

#### Admin Panel (Port 3002)

```bash
# Open in browser
open http://localhost:3002
```

#### Vendor Panel (Port 3003)

```bash
# Open in browser
open http://localhost:3003
```

---

### STEP 7: Database Initialization

```bash
# Access database container
docker-compose exec postgres psql -U postgres -d ecommerce

# Check tables (inside psql)
\dt

# Exit psql
\q
```

---

### STEP 8: Access Applications

| Application | URL | Purpose |
|-------------|-----|---------|
| **Customer Portal** | http://localhost:3001 | Shopping, Browsing Products |
| **Admin Panel** | http://localhost:3002 | Manage Platform, Users, Orders |
| **Vendor Panel** | http://localhost:3003 | Manage Store, Products, Orders |
| **Backend API** | http://localhost:3000 | API Endpoints |
| **Redis CLI** | http://localhost:6379 | Cache Management |
| **Database** | localhost:5432 | PostgreSQL |

---

## Testing API Endpoints

### Using Postman (Recommended)

1. Download Postman: https://www.postman.com/downloads/
2. Import collection from `docs/postman-collection.json`
3. Set variables and test endpoints

### Using cURL Commands

```bash
# 1. Register User
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
  }'

# 2. Login User
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
# Save the token from response

# 3. Get Products (Public)
curl http://localhost:3000/api/products

# 4. Create Product (Requires Auth)
curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "name": "Laptop",
    "description": "Gaming Laptop",
    "price": 1299.99,
    "stock": 50,
    "categoryId": "uuid-here"
  }'

# 5. Get Orders
curl http://localhost:3000/api/orders \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# 6. Create Order
curl -X POST http://localhost:3000/api/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "userId": "user-uuid",
    "items": [{
      "productId": "product-uuid",
      "quantity": 2,
      "price": 99.99
    }],
    "totalAmount": 199.98,
    "shippingAddress": "123 Main St, City, State",
    "paymentMethod": "credit_card"
  }'
```

---

## Common Docker Commands

```bash
# Start services
docker-compose up -d

# Stop services (data persists)
docker-compose stop

# Start stopped services
docker-compose start

# Stop and remove containers (data persists due to volumes)
docker-compose down

# Stop, remove containers AND volumes (DATA LOSS)
docker-compose down -v

# View logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f backend

# Execute command in container
docker-compose exec backend npm run dev

# Access container shell
docker-compose exec backend sh

# Access database
docker-compose exec postgres psql -U postgres -d ecommerce

# Restart service
docker-compose restart backend

# Rebuild images
docker-compose build --no-cache

# Remove unused images/volumes
docker system prune -a
```

---

## Troubleshooting

### Issue: Port Already in Use

```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>

# For Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Issue: Database Connection Error

```bash
# Check if PostgreSQL container is running
docker-compose ps postgres

# View database logs
docker-compose logs postgres

# Restart database
docker-compose restart postgres
```

### Issue: Frontend Not Loading

```bash
# Check if frontend container is running
docker-compose ps frontend

# View frontend logs
docker-compose logs frontend

# Rebuild frontend
docker-compose build frontend
docker-compose up -d frontend
```

### Issue: Backend API Not Responding

```bash
# Check backend logs
docker-compose logs backend

# Test connection
curl http://localhost:3000/health

# Restart backend
docker-compose restart backend
```

### Issue: Node Modules Issues

```bash
# Remove and reinstall
docker-compose down
docker volume prune -f
docker-compose up -d
```

---

## Development Workflow

### Making Backend Changes

```bash
# Backend automatically reloads (watch mode enabled)
# Edit files in backend/src/
# Changes reflected immediately at http://localhost:3000
```

### Making Frontend Changes

```bash
# Frontend automatically reloads (Next.js dev mode)
# Edit files in frontend/src/
# Changes reflected immediately at http://localhost:3001
```

### Running Tests

```bash
# Run backend tests
docker-compose exec backend npm run test

# Run frontend tests
docker-compose exec frontend npm run test

# Run with coverage
docker-compose exec backend npm run test:cov
```

---

## Production Deployment

```bash
# Build production images
docker-compose -f docker-compose.yml build

# Use docker-compose.prod.yml for production
# Update environment variables in .env.production

# Deploy to cloud (AWS, Azure, Google Cloud, etc.)
# See deployment guides in docs/
```

---

## Performance Optimization

- Redis caching enabled for products, orders
- Database indexes on frequently queried columns
- CDN for static assets
- Image optimization and lazy loading
- API rate limiting (coming soon)

---

## Monitoring & Logs

```bash
# Real-time logs
docker-compose logs -f

# Historical logs
docker-compose logs --tail=100

# Export logs
docker-compose logs > app-logs.txt
```

---

## Next Steps

1. ✅ System is running locally
2. 📝 Create test accounts in admin panel
3. 🛍️ Add sample products
4. 💳 Configure payment gateways
5. 📱 Build mobile apps (Flutter)
6. 🚀 Deploy to production

---

## Need Help?

- Documentation: Check `docs/` folder
- Issues: GitHub Issues
- Contact: Email support

---

**Happy Coding! 🎉**
