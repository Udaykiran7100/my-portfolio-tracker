# Simple Portfolio Tracker

## Overview

The *Simple Portfolio Tracker* is a full-stack application that allows users to track their portfolio of assets (stocks, cryptocurrencies, etc.). Users can register, log in, view and update their portfolios, and record transactions such as buying and selling assets. This application includes both a *frontend* built with *React* and a *backend* built with *Node.js* and *Express*.

## Features

- *User Authentication*: Secure login and registration with JWT-based authentication.
- *Portfolio Management*: Users can view and update their portfolio with new assets.
- *Transaction Tracking*: Users can track their buy/sell transactions, including asset symbol, quantity, and price.
- *Market Price Integration*: Asset prices are fetched in real-time from external APIs (e.g., CoinGecko for cryptocurrencies).
- *Profit/Loss Calculation*: Automatically calculates the value of the portfolio based on the latest market prices.

## Tech Stack

- *Frontend*:
  - *React*: JavaScript library for building user interfaces.
  - *Axios*: HTTP client for making API requests to the backend.
  - *React Router*: For managing navigation and routes in the frontend.

- *Backend*:
  - *Node.js*: JavaScript runtime for building the backend.
  - *Express*: Web framework for building the API.
  - *Sequelize*: ORM for database management with PostgreSQL.
  - *JWT*: JSON Web Tokens for user authentication.
  - *Axios*: HTTP client for fetching real-time asset prices from external APIs.
  - *PostgreSQL*: Database for storing user, portfolio, and transaction data.

## Prerequisites

Before running the application, make sure you have the following installed:

- Node.js (v16 or above)
- PostgreSQL
- npm or yarn

## Installation

### 1. Clone the repository
bash
git clone https://github.com/yourusername/simple-portfolio-tracker.git
cd simple-portfolio-tracker


### 2. Install Dependencies

#### Backend

Navigate to the backend folder and install the backend dependencies:
bash
cd backend
npm install


#### Frontend

Navigate to the frontend folder and install the frontend dependencies:
bash
cd frontend
npm install


### 3. Set up environment variables

Create a .env file in the root directory (backend) with the following contents:
env
DB_URL=postgres://yourusername:yourpassword@localhost:5432/portfolio
JWT_SECRET=yoursecretkey


Make sure to replace yourusername, yourpassword, and yoursecretkey with your actual values.

### 4. Set up the database

Ensure that your PostgreSQL database is set up and running. You can create the database using the following command:
bash
psql -U postgres -c "CREATE DATABASE portfolio;"


### 5. Run Migrations (Backend)

Ensure the database schema is up-to-date by running the migrations:
bash
cd backend
node server.js


This will automatically create the necessary tables in your PostgreSQL database.

### 6. Run the Backend Server

Start the backend server:
bash
cd backend
npm start


The backend server will be running on http://localhost:5000.

### 7. Run the Frontend Development Server

Start the frontend server:
bash
cd frontend
npm start


The frontend will be running on http://localhost:3000.

## Directory Structure

### Backend Folder Structure


backend/
├── controllers/
│   ├── authController.js
│   ├── portfolioController.js
│   └── transactionController.js
├── models/
│   ├── userModel.js
│   ├── portfolioModel.js
│   └── transactionModel.js
├── routes/
│   ├── authRoutes.js
│   ├── portfolioRoutes.js
│   └── transactionRoutes.js
├── services/
│   ├── authService.js
│   ├── portfolioService.js
│   └── assetService.js
├── middlewares/
│   ├── authMiddleware.js
│   └── errorMiddleware.js
├── config/
│   ├── dbConfig.js
│   └── envConfig.js
├── utils/
│   ├── apiHelper.js
│   └── validation.js
├── migrations/
│   ├── 202312250001-create-user.js
│   ├── 202312250002-create-portfolio.js
│   └── 202312250003-create-transaction.js
├── seeds/
│   ├── seedUsers.js
│   └── seedPortfolios.js
├── tests/
│   ├── portfolio.test.js
│   ├── auth.test.js
│   └── transaction.test.js
├── .env
├── package.json
└── server.js


### Frontend Folder Structure


frontend/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── Portfolio.js
│   │   ├── Transaction.js
│   │   └── Auth.js
│   ├── services/
│   │   └── apiService.js
│   ├── App.js
│   ├── App.css
│   └── index.js
├── .env
├── package.json
└── README.md


## API Endpoints (Backend)

### 1. *User Authentication*

- *POST /auth/register*: Register a new user.
  - Body: { "email": "user@example.com", "password": "password123" }
  - Response: { "token": "JWT_TOKEN" }

- *POST /auth/login*: Log in a user.
  - Body: { "email": "user@example.com", "password": "password123" }
  - Response: { "token": "JWT_TOKEN" }

### 2. *Portfolio Management*

- *GET /portfolio*: Get the user's portfolio and its calculated value.
  - Headers: { "Authorization": "Bearer JWT_TOKEN" }
  - Response: { "portfolio": {...}, "portfolioValue": 10000 }

- *PUT /portfolio*: Update the user's portfolio.
  - Headers: { "Authorization": "Bearer JWT_TOKEN" }
  - Body: { "assets": [{"assetSymbol": "BTC", "quantity": 1.5, "price": 45000 }] }
  - Response: { "portfolio": {...} }

### 3. *Transactions*

- *POST /transactions*: Create a new buy/sell transaction.
  - Headers: { "Authorization": "Bearer JWT_TOKEN" }
  - Body: { "assetSymbol": "BTC", "quantity": 1, "price": 45000 }
  - Response: { "transaction": {...} }

- *GET /transactions*: Get the user's transaction history.
  - Headers: { "Authorization": "Bearer JWT_TOKEN" }
  - Response: [ { "assetSymbol": "BTC", "quantity": 1, "price": 45000, "date": "2024-12-23T12:00:00Z" } ]

## Frontend Overview

### Components

- *Portfolio*: Displays the user's portfolio and its value.
- *Transaction*: Allows the user to add new transactions (buy/sell).
- *Auth*: Handles user login and registration.

### Services

- *apiService.js*: Contains the Axios HTTP requests to interact with the backend API.

### Example of Using the API in React

#### src/services/apiService.js
js
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:5000',
});

export const registerUser = async (userData) => {
  const response = await api.post('/auth/register', userData);
  return response.data;
};

export const loginUser = async (userData) => {
  const response = await api.post('/auth/login', userData);
  return response.data;
};

export const fetchPortfolio = async (token) => {
  const response = await api.get('/portfolio', {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });
  return response.data;
};


#### src/components/Portfolio.js
js
import React, { useEffect, useState } from 'react';
import { fetchPortfolio } from '../services/apiService';

const Portfolio = ({ token }) => {
  const [portfolio, setPortfolio] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      const data = await fetchPortfolio(token);
      setPortfolio(data);
    };

    fetchData();
  }, [token]);

  if (!portfolio) return <div>Loading...</div>;

  return (
    <div>
      <h2>Your Portfolio</h2>
      <div>
        {portfolio.assets.map((asset, index) => (
          <div key={index}>
            <p>{asset.assetSymbol}: {asset.quantity} @ ${asset.price}</p>
          </div>
        ))}
      </div>
      <h3>Total Portfolio Value: ${portfolio.portfolioValue}</h3>
    </div>
  );
};

export default Portfolio;


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
