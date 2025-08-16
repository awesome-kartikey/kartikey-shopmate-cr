# Shopmate Application Architecture

This document provides an overview of the Shopmate application's architecture, including its structure, components, data flow, and key design decisions.

## 1. System Architecture

Shopmate is a **client-side single-page application (SPA)** built entirely with React. It does not have a dedicated backend or database. All product data is hardcoded, and the application state (primarily the shopping cart) resides within the browser's memory during a user session.

- **Frontend Framework:** React.js
- **State Management:** React Context API + `useReducer`
- **Routing:** React Router DOM
- **Structure:** Component-based architecture typical of React applications.

## 2. Project Folder Structure

The project follows a structure largely based on Create React App conventions, with specific organization for application source code:

```
kartikey-shopmate-cr/
├── README.md           # Project overview (This needs to be replaced)
├── package.json        # Project dependencies and scripts
├── public/             # Static assets and index.html template
│   ├── index.html      # Main HTML entry point
│   └── assets/
│       └── images/     # Product images
└── src/                # Application source code
    ├── App.css         # App-level component styles
    ├── App.js          # Root application component
    ├── index.css       # Global styles
    ├── index.js        # Application entry point (renders App, sets up Router & Context)
    ├── assets/         # Frontend assets (e.g., logo)
    ├── components/     # Reusable UI components
    │   ├── CartCard.js # Component for displaying an item in the cart
    │   ├── Header.js   # Application header/navigation
    │   ├── ProductCard.js # Component for displaying a product on the home page
    │   └── index.js    # Barrel file for exporting components
    ├── context/
    │   └── CartContext.js # Defines Cart Context, Provider, and useCart hook
    ├── hooks/
    │   └── useTitle.js # Custom hook for setting the document title
    ├── pages/          # Page-level components corresponding to routes
    │   ├── Cart.js     # Cart page component
    │   ├── Home.js     # Home page component (product listing)
    │   └── index.js    # Barrel file for exporting pages
    ├── reducer/
    │   └── cartReducer.js # Reducer function for managing cart state logic
    └── routes/
        └── AllRoutes.js # Defines application routing rules
```

- **`public/`**: Contains the base HTML file and static assets like product images.
- **`src/`**: Contains all the React code.
  - **`components/`**: Dumb/Presentational or smart components used across different pages or complex enough to warrant separation (e.g., `ProductCard`, `Header`).
  - **`pages/`**: Top-level components representing distinct views/pages of the application (`Home`, `Cart`).
  - **`context/`**: Holds the implementation for the React Context API, specifically for managing the global cart state.
  - **`hooks/`**: Contains custom React hooks (`useTitle`).
  - **`reducer/`**: Contains the reducer function used by `CartContext` to manage state updates.
  - **`routes/`**: Defines the mapping between URL paths and page components.
  - **`App.js`**: The main application shell, rendering the `Header` and the routes.
  - **`index.js`**: The application's entry point, responsible for rendering the `App` component into the DOM and wrapping it with necessary providers (`BrowserRouter`, `CartProvider`).

## 3. Major Components

- **`App.js`**: The root component. It renders the persistent `Header` and the dynamic page content managed by `AllRoutes`.
- **`Header.js`**: Displays the application title/logo, navigation links (`Home`, `Cart`), and the current count of items in the cart (obtained via `useCart`).
- **`AllRoutes.js`**: Uses `react-router-dom` to define which page component (`Home` or `Cart`) should be rendered based on the current URL.
- **`Home.js`**: The landing page. It fetches (currently hardcoded) product data and renders a list of `ProductCard` components. It also uses the `useTitle` hook.
- **`Cart.js`**: Displays the list of items currently in the shopping cart using `CartCard` components. It shows the total number of items and the calculated total price from the `CartContext`. It also uses the `useTitle` hook.
- **`ProductCard.js`**: A reusable component to display individual product details (image, name, price) and provides "Add To Cart" / "Remove" functionality by interacting with the `CartContext` via the `useCart` hook. It uses local state (`useState`) to track if the item is currently in the cart for button display logic.
- **`CartCard.js`**: A reusable component used on the Cart page to display details of an item in the cart, including a "Remove" button that interacts with the `CartContext`.
- **`CartProvider` (in `CartContext.js`)**: A context provider component that wraps the application (in `index.js`). It uses `useReducer` with `cartReducer` to manage the `cartList` and `total` state and provides this state along with update functions (`addToCart`, `removeFromCart`) to any component within its tree via the `useCart` hook.
- **`useTitle.js`**: A custom hook encapsulating the logic to update the browser tab's title.

## 4. Data Flow

The primary data flow revolves around the shopping cart state managed by `CartContext`.

1.  **Initialization:** `CartProvider` initializes the state (`cartList`, `total`) using `useReducer` with `cartReducer` and the `initialState`.
2.  **Displaying Data:**
    - `Home.js` displays hardcoded `products`.
    - `ProductCard.js` receives `product` data as props. It checks `cartList` from `useCart()` to determine if the product is already in the cart (`isInCart` state).
    - `Cart.js` gets `cartList` and `total` from `useCart()` and maps over `cartList` to render `CartCard` components.
    - `Header.js` gets `cartList.length` from `useCart()` to display the item count.
3.  **Updating Cart (Adding):**
    - User clicks "Add To Cart" on a `ProductCard`.
    - The `onClick` handler in `ProductCard.js` calls `addToCart(product)` obtained from `useCart()`.
    - `addToCart` in `CartContext.js` creates the `updatedCartList`.
    - It calls `updateTotal(updatedCartList)` which calculates the new total.
    - `updateTotal` dispatches an action `{ type: "UPDATE_TOTAL", payload: { total } }` to `cartReducer`.
    - `addToCart` dispatches an action `{ type: "ADD_TO_CART", payload: { products: updatedCartList } }` to `cartReducer`.
    - `cartReducer` handles these actions and returns the new state `{ cartList, total }`.
    - `CartProvider` re-renders, providing the new state value.
    - Components consuming `useCart()` (Header, Home, Cart, ProductCard) re-render with the updated information.
4.  **Updating Cart (Removing):**
    - User clicks "Remove" on a `ProductCard` or `CartCard`.
    - The `onClick` handler calls `removeFromCart(product)` obtained from `useCart()`.
    - `removeFromCart` in `CartContext.js` filters the `cartList`.
    - It calls `updateTotal()` on the filtered list, which dispatches "UPDATE_TOTAL".
    - It dispatches an action `{ type: "REMOVE_FROM_CART", payload: { products: filteredList } }` to `cartReducer`.
    - `cartReducer` updates the state.
    - `CartProvider` re-renders, providing the new state.
    - Consuming components re-render.

## 5. Design Decisions

- **Context API + useReducer for State Management:** Chosen for managing global state (cart) shared across multiple components (Header, Home, Cart, ProductCard). It's a built-in React solution suitable for small-to-medium complexity state, avoiding the need for external libraries like Redux or Zustand for this simple case. `useReducer` is preferred over `useState` here because the state logic (adding, removing, calculating total) is slightly more complex and involves related state pieces (`cartList` and `total`).
- **Custom `useCart` Hook:** Simplifies accessing the Cart context, providing a clean interface (`const { cartList, total, addToCart, removeFromCart } = useCart();`) for components.
- **Custom `useTitle` Hook:** Encapsulates the side effect of updating the document title, making page components cleaner and promoting reusability.
- **Hardcoded Product Data:** Used for simplicity in this frontend-focused example. In a real application, this data would typically be fetched from a backend API.
- **Component-Based Structure:** Follows standard React practices, breaking down the UI into reusable and manageable components.
- **Plain CSS:** Simple styling approach. CSS files are co-located or associated with components/pages. For larger apps, CSS Modules, Styled Components, or utility-first libraries like Tailwind CSS might be considered for better scoping and maintainability.
- **Barrel Files (`index.js` in folders):** Used in `components` and `pages` for cleaner imports (e.g., `import { Header, ProductCard } from "./components"`).
