# Shopmate - Simple Shopping Cart

A basic React application demonstrating a simple e-commerce shopping cart functionality using Context API and useReducer for state management.

## Description

Shopmate is a frontend-only web application that allows users to browse a list of products, add them to a shopping cart, view the cart contents, and remove items from the cart. It showcases fundamental React concepts including components, state management with Context API and reducers, routing, and custom hooks.

## Features

- View a list of available products on the Home page.
- Add products to the shopping cart.
- View items added to the cart on the Cart page.
- See the total number of items and the total price in the cart.
- Remove items from the cart (from both the Home page and the Cart page).
- Dynamic page titles based on the current route.
- Responsive header showing cart count.

## Tech Stack

- **Frontend:** React.js (v18)
- **Routing:** React Router DOM (v6)
- **State Management:** React Context API & `useReducer` hook
- **Styling:** Plain CSS
- **Build Tool:** Create React App

## Setup Instructions

1.  **Clone the repository:**

    ```bash
    git clone <repository-url>
    cd kartikey-shopmate-cr
    ```

2.  **Install dependencies:**

    ```bash
    npm install
    ```

3.  **Run the application:**
    ```bash
    npm start
    ```
    This will start the development server, and you can view the application at `http://localhost:3000`.

## Usage

1.  Navigate to the **Home** page (`/`) to see the list of products.
2.  Click the "Add To Cart" button on a product card to add it to your shopping cart. The button will change to "Remove" if the item is in the cart.
3.  Click the "Remove" button on a product card (on the Home page) or in the cart list (on the Cart page) to remove an item.
4.  Click the **Cart** link in the header or navigate to (`/cart`) to view the items currently in your cart.
5.  The header always displays the current number of items in the cart.
6.  The Cart page displays the total price of all items in the cart.
