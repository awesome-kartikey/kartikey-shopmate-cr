# Frequently Asked Questions (FAQ)

Here are answers to some common questions about the Shopmate project.

**Q1: How is the shopping cart state managed?**

**A:** The cart state (list of items and total price) is managed globally using React's Context API (`CartContext.js`) combined with the `useReducer` hook (`cartReducer.js`). This avoids prop drilling and centralizes the cart logic.

*   `CartContext.js`: Creates the context and provides the state (`cartList`, `total`) and dispatch functions (`addToCart`, `removeFromCart`) to consuming components via the `useCart` custom hook.
*   `cartReducer.js`: Contains the pure functions that handle state transitions based on dispatched actions (ADD\_TO\_CART, REMOVE\_FROM\_CART, UPDATE\_TOTAL).

**Q2: Where does the product data come from? Is there a backend API?**

**A:** Currently, the product data is hardcoded directly within the `Home.js` component as an array of JavaScript objects. There is no backend API connected to this project; it's purely a frontend demonstration.

```javascript
// src/pages/Home.js
const products = [
  {"id": 1, "name": "Sony Wh-Ch510 Bluetooth Wireless", "price": 149, "image": "/assets/images/1001.png"},
  // ... more products
];
```

**Q3: How can I add more products?**

**A:** To add more products, you need to manually edit the `products` array within the `src/pages/Home.js` file. Make sure to provide a unique `id`, `name`, `price`, and the correct path to the product `image` (place the image file in the `public/assets/images/` directory).

**Q4: How is routing handled in the application?**

**A:** Routing is managed using the `react-router-dom` library (version 6).

*   `src/index.js`: Wraps the entire `App` component with `<BrowserRouter>`.
*   `src/routes/AllRoutes.js`: Defines the application routes using `<Routes>` and `<Route>`. It maps URL paths (`/` and `/cart`) to their corresponding page components (`Home` and `Cart`).
*   `src/components/Header.js`: Uses `<Link>` and `<NavLink>` components from `react-router-dom` for navigation between pages.

**Q5: What does the `useTitle` custom hook do?**

**A:** The `useTitle` hook (`src/hooks/useTitle.js`) is a simple custom hook that sets the document's title (visible in the browser tab). It takes a `title` string as an argument and uses the `useEffect` hook to update `document.title` whenever the title prop changes. This helps improve user experience and SEO by having descriptive page titles.

```javascript
// src/hooks/useTitle.js
useEffect(() => {
  document.title = `${title} | Shopping Cart`;
}, [title]);
```

**Q6: How is styling applied?**

**A:** Styling is done using plain CSS.

*   Global styles are defined in `src/index.css`.
*   App-level layout styles are in `src/App.css`.
*   Component-specific styles are located in corresponding CSS files within the `src/components/` directory (e.g., `Header.css`, `ProductCard.css`, `CartCard.css`). These are imported directly into their respective JavaScript component files.

**Q7: Are the cart items saved if I close the browser?**

**A:** No. Since the state is managed entirely within React's client-side memory and there's no backend or local storage persistence implemented, the cart will be empty when the page is refreshed or the browser is closed.