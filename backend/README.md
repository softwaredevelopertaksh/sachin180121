# Backend Service - NestJS API

Multi-Vendor E-Commerce Platform Backend built with NestJS

## Core Modules

### Authentication Module
- User Registration & Login
- JWT Token Management
- Password Hashing with bcrypt
- Role-Based Access Control

### Products Module
- Product CRUD Operations
- Product Search & Filtering
- Inventory Management
- Product Variants Support

### Orders Module
- Order Creation & Management
- Order Status Tracking
- Order History
- Return/Refund Management

### Vendors Module
- Vendor Registration & KYC
- Vendor Dashboard
- Commission Management
- Performance Analytics

### Payment Module
- Payment Processing
- Payment Verification
- Refund Handling
- Multiple Gateway Support

### Categories Module
- Category Management
- Hierarchical Categories
- Brand Management

## Installation

```bash
npm install
npm run dev
```

## Environment Setup

```
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=ecommerce
JWT_SECRET=your_secret
```
