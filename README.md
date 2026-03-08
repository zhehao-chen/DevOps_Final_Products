# Product Service

REST API for product catalog management.

## Features
- Product CRUD operations
- Stock management
- Product search and filtering

## API Endpoints

### GET /health
Health check endpoint

### GET /api/products
Get all active products

### GET /api/products/{id}
Get specific product by ID

### POST /api/products
Create new product
```json
{
  "name": "Product Name",
  "description": "Product Description",
  "price": 29.99,
  "stock": 100
}
```

### PUT /api/products/{id}
Update product details

### PATCH /api/products/{id}/stock
Update product stock
```json
{
  "quantity": 10
}
```

## Setup
```bash
pip install -r requirements.txt
```

## Environment Variables
- `DB_HOST` - Database host (default: localhost)
- `DB_PORT` - Database port (default: 5432)
- `DB_NAME` - Database name (default: ecommerce)
- `DB_USER` - Database user (default: postgres)
- `DB_PASSWORD` - Database password (default: postgres)

## Run
```bash
python app.py
```

Service runs on port 5001
