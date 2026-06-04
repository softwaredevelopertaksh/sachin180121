# Complete Local Setup Guide

## Prerequisites

- Docker & Docker Compose installed
- Node.js 18+ (for local development without Docker)
- Git
- PostgreSQL client tools (optional)

## Quick Start with Docker (Recommended)

### 1. Clone the Repository

```bash
git clone https://github.com/softwaredevelopertaksh/sachin180121.git
cd sachin180121
```

### 2. Start All Services with Docker Compose

```bash
# Build and start all containers
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down
```

### Services Running Locally

After `docker-compose up -d`, these services will be available:

| Service | URL | Port | Purpose |
|---------|-----|------|---------|
| **Backend API** | http://localhost:3000 | 3000 | NestJS API |
| **Frontend (Customer Portal)** | http://localhost:3001 | 3001 | React/Next.js Web App |
| **Admin Panel** | http://localhost:3002 | 3002 | Admin Dashboard |
| **Vendor Panel** | http://localhost:3003 | 3003 | Vendor Dashboard |
| **PostgreSQL** | localhost:5432 | 5432 | Database |
| **Redis** | localhost:6379 | 6379 | Cache |

---

## Local Setup Without Docker

### Backend Setup

```bash
cd backend

# Install dependencies
npm install

# Create .env file
cp .env.example .env

# Update .env with your local database credentials
# DB_HOST=localhost
# DB_PORT=5432
# DB_USERNAME=postgres
# DB_PASSWORD=postgres

# Run database migrations
npm run migration:run

# Start development server
npm run dev
```

Backend will run on: **http://localhost:3000**

### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create .env.local file
echo "NEXT_PUBLIC_API_URL=http://localhost:3000/api" > .env.local

# Start development server
npm run dev
```

Frontend will run on: **http://localhost:3001**

### Admin Panel Setup

```bash
cd admin-panel

# Install dependencies
npm install

# Create .env.local file
echo "NEXT_PUBLIC_API_URL=http://localhost:3000/api" > .env.local

# Start development server
npm run dev
```

Admin Panel will run on: **http://localhost:3002**

### Vendor Panel Setup

```bash
cd vendor-panel

# Install dependencies
npm install

# Create .env.local file
echo "NEXT_PUBLIC_API_URL=http://localhost:3000/api" > .env.local

# Start development server
npm run dev
```

Vendor Panel will run on: **http://localhost:3003**

---

## Database Setup

### Option 1: Using Docker (Recommended)

PostgreSQL automatically starts in Docker Compose

### Option 2: Local PostgreSQL Installation

```bash
# Install PostgreSQL (macOS)
brew install postgresql

# Install PostgreSQL (Ubuntu)
sudo apt-get install postgresql

# Start PostgreSQL service
brew services start postgresql

# Create database
createdb ecommerce

# Create user
psql -U postgres -d postgres -c "CREATE USER ecommerce WITH PASSWORD 'ecommerce';"

# Grant privileges
psql -U postgres -d postgres -c "ALTER USER ecommerce SUPERUSER;"
```

---

## API Testing

### Using Postman

1. Import the Postman collection from `docs/postman-collection.json`
2. Set environment variables:
   - `BASE_URL=http://localhost:3000`
   - `TOKEN=your_jwt_token`

### Using cURL

```bash
# Health Check
curl http://localhost:3000/api/health

# Register User
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
  }'

# Login
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'

# Get Products
curl http://localhost:3000/api/products

# Create Product
curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "name": "Product Name",
    "description": "Product Description",
    "price": 99.99,
    "stock": 10,
    "categoryId": "category-uuid"
  }'
```

---

## Common Commands

### Docker Compose Commands

```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f backend

# Rebuild containers
docker-compose build

# Remove volumes (CAREFUL - deletes database)
docker-compose down -v

# Access backend container
docker-compose exec backend sh

# Access database container
docker-compose exec postgres psql -U postgres -d ecommerce
```

### Development Commands

```bash
# Run tests
npm run test

# Run linting
npm run lint

# Build for production
npm run build

# Run in production mode
npm start
```

---

## Troubleshooting

### Port Already in Use

```bash
# Find and kill process on port 3000
lsof -ti:3000 | xargs kill -9

# For Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Database Connection Error

```bash
# Check PostgreSQL is running
docker-compose ps postgres

# View PostgreSQL logs
docker-compose logs postgres

# Reset database
docker-compose down -v
docker-compose up -d
```

### Node Modules Issues

```bash
# Remove node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

### Docker Issues

```bash
# Clear Docker cache
docker system prune -a

# Rebuild all containers
docker-compose build --no-cache
docker-compose up -d
```

---

## Environment Variables

### Backend (.env)

```env
NODE_ENV=development
PORT=3000
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=ecommerce
JWT_SECRET=your_jwt_secret_key_here
REDIS_HOST=localhost
REDIS_PORT=6379
```

### Frontend (.env.local)

```env
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_STRIPE_KEY=your_stripe_public_key
NEXT_PUBLIC_RAZORPAY_KEY=your_razorpay_key
```

---

## Project Structure

```
sachin180121/
├── backend/                 # NestJS API
│   ├── src/
│   │   ├── modules/        # Feature modules
│   │   ├── main.ts         # Entry point
│   │   └── app.module.ts   # Main module
│   ├── Dockerfile
│   └── package.json
├── frontend/               # React/Next.js Customer Portal
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── admin-panel/            # Admin Dashboard
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── vendor-panel/           # Vendor Dashboard
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml      # Docker Compose configuration
├── .gitignore
└── README.md
```

---

## Next Steps

1. **Database Schema**: Define TypeORM entities for all models
2. **API Endpoints**: Implement REST endpoints for all features
3. **Authentication**: Implement JWT-based auth
4. **Payment Integration**: Integrate Razorpay/Stripe
5. **Frontend Pages**: Build React components
6. **Admin Features**: Implement admin dashboard
7. **Vendor Features**: Implement vendor panel
8. **Testing**: Add unit and integration tests
9. **Deployment**: Set up CI/CD pipeline

---

## Support & Documentation

- API Documentation: http://localhost:3000/api/docs
- Database Schema: `docs/database-schema.md`
- API Reference: `docs/api-reference.md`
- Contributing Guide: `CONTRIBUTING.md`

---

## License

This project is licensed under the MIT License.
