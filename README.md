- Download Raw data then you will get all files
# Food App API (Node.js + Express + MongoDB)

A RESTful backend API for a food-ordering application. Provides user authentication, role-based access control, and CRUD endpoints for restaurants, food items, categories and orders. Built with Node.js, Express and MongoDB (Mongoose).

## Features

- User registration and login (bcrypt + JWT)
- Role-based access control (admin, client, vendor, driver)
- CRUD for Restaurants, Foods (menu items) and Categories
- Place orders (cart aggregation) and admin-updatable order status
- Password reset and change-password endpoints
- Request logging (morgan), CORS and environment-driven configuration (dotenv)

## Tech Stack

- Node.js
- Express
- MongoDB with Mongoose
- JSON Web Tokens (JWT)
- bcryptjs for password hashing
- dotenv, cors, morgan, nodemon

## Quick start (Windows / PowerShell)

1. Clone the repo and install dependencies

```powershell
git clone <your-repo-url>
cd Resturant_Food_App_NodeJS
npm install
```

2. Create a `.env` file in the project root with the following variables:

```ini
# .env
MONGO_URL=mongodb+srv://<user>:<password>@cluster0.mongodb.net/<dbname>?retryWrites=true&w=majority
JWT_SECRET=your_jwt_secret_here
PORT=5000
```

3. Run the server (development)

```powershell
npm run server
```

The API will be available at: http://localhost:5000 (or the PORT you set).

## API – Endpoints overview
- NOTE: If any api(given below) is not present, once try with those. 

Base path: `/api/v1`

Auth
- POST `/auth/register` — Register a new user. Body: { userName, email, password, phone, address, answer }
- POST `/auth/login` — Login and receive JWT. Body: { email, password }

User
- GET `/user/getUser` — Get authenticated user profile (requires Bearer token)
- PUT `/user/updateUser` — Update profile (requires Bearer token)
- POST `/user/updatePassword` — Change password (requires Bearer token)
- POST `/user/resetPassword` — Reset password using security answer (requires Bearer token)
- DELETE `/user/deleteUser/:id` — Delete user (requires Bearer token)

Restaurants
- POST `/resturant/create` — Create a restaurant (requires Bearer token)
- GET `/resturant/getAll` — Get all restaurants
- GET `/resturant/get/:id` — Get restaurant by id
- DELETE `/resturant/delete/:id` — Delete restaurant (requires Bearer token)

Categories
- POST `/category/create` — Create category (requires Bearer token)
- GET `/category/getAll` — Get all categories
- PUT `/category/update/:id` — Update category (requires Bearer token)
- DELETE `/category/delete/:id` — Delete category (requires Bearer token)

Foods (Menu Items)
- POST `/food/create` — Create food item (requires Bearer token)
- GET `/food/getAll` — Get all food items
- GET `/food/get/:id` — Get single food item by id
- GET `/food/getByResturant/:id` — Get foods by restaurant id
- PUT `/food/update/:id` — Update food (requires Bearer token)
- DELETE `/food/delete/:id` — Delete food (requires Bearer token)
- POST `/food/placeorder` — Place an order (requires Bearer token). Body: { cart: [ { price, ... } ], id: <buyerId> }
- POST `/food/orderStatus/:id` — Update order status (requires Bearer token + admin role)

Test
- GET `/test/test-user` — Simple test route

Notes on Authorization
- Most protected routes require an `Authorization: Bearer <token>` header. The `authMiddleware` verifies the token and attaches the user id to `req.body.id`.
- The `adminMiddleware` checks the `usertype` of the user (must be `admin`) for admin-only endpoints.

## Example: Register and Login (curl)

Register

```bash
curl -X POST http://localhost:5000/api/v1/auth/register \
	-H "Content-Type: application/json" \
	-d '{"userName":"Alice","email":"alice@example.com","password":"password123","phone":"1234567890","address":"[\"12 Main St\"]","answer":"pet"}'
```

Login

```bash
curl -X POST http://localhost:5000/api/v1/auth/login \
	-H "Content-Type: application/json" \
	-d '{"email":"alice@example.com","password":"password123"}'
```

Use the returned `token` in the `Authorization` header for protected requests:

```
Authorization: Bearer <token>
```

## Data models (high level)

- User — { userName, email, password (hashed), phone, address, usertype, profile, answer }
- Resturant — { title, imageUrl, foods, time, pickup, delivery, coords, rating }
- Foods — { title, description, price, imageUrl, catgeory, resturnat (ObjectId), rating }
- Category — { title, imageUrl }
- Orders — { foods: [ObjectId], payment, buyer, status }

## Known limitations & recommended improvements

- Input validation: add schema validation (Joi or express-validator) to avoid malformed requests.
- Error handling: add centralized error handler and consistent response shapes.
- Security: implement rate-limiting, request sanitization, and consider refresh tokens for JWT.
- Tests & CI: add Jest + Supertest tests and GitHub Actions for CI.
- Documentation: add OpenAPI/Swagger for interactive API docs.

## Development tips

- Use `nodemon` for fast iteration (`npm run server`).
- Ensure `MONGO_URL` and `JWT_SECRET` are set in `.env` before starting.

## License

This project uses the ISC license (see `package.json`).

