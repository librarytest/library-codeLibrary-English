---
  title: 'MobileNavbar'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # MobileNavbar.jsx
  # Explanation of MobileNavbar Component

The provided code snippet is a React functional component called `MobileNavbar`. This component is responsible for rendering a mobile-friendly navigation menu that can be toggled open and closed.

### Dependencies
- **React**: The code uses React for building user interfaces.
- **react-router-dom**: Used for routing within the React application.
- **@heroicons/react**: Provides icons for the navigation menu.

### Components and Functions
1. **useState**: This is a React hook used to manage state within functional components. In this code, it is used to toggle the visibility of the mobile navigation menu.
   
2. **Link**: A component from `react-router-dom` used to create links within the application.

3. **Bars4Icon, XMarkIcon**: Icons imported from `@heroicons/react` library. These icons are used to represent the open and close states of the mobile menu.

4. **toggleMenu**: This function toggles the `isOpen` state between `true` and `false` when the menu button is clicked.

### Component Structure
- The component takes an array of `navItems` as a prop, which contains objects with `text` and `href` properties for each navigation item.
- When the menu button is clicked, the `toggleMenu` function is called to toggle the visibility of the menu.
- The menu button displays either the hamburger icon (`Bars4Icon`) or the close icon (`XMarkIcon`) based on the `isOpen` state.
- The mobile menu is displayed as a full-screen overlay with a black background that fades in and out based on the `isOpen` state.
- Each navigation item is rendered as a list item (`li`) with a link (`Link`) to the specified `href`.
- Clicking on a navigation item closes the mobile menu by setting `isOpen` to `false`.

### Example Usage
```jsx
import MobileNavbar from './MobileNavbar';

const App = () => {
  const navItems = [
    { text: 'Home', href: '/' },
    { text: 'About', href: '/about' },
    { text: 'Contact', href: '/contact' },
  ];

  return (
    <div>
      <MobileNavbar navItems={navItems} />
      {/* Other components and content */}
    </div>
  );
};

export default App;
```

In the example above, the `MobileNavbar` component is used within an `App` component with an array of navigation items passed as a prop. This will render a mobile navigation menu with the specified items.
  
  ## Component Code
  ```jsx
  "use client";
import { useState } from "react";
import { Link } from "react-router-dom";
import { Bars4Icon, XMarkIcon } from "@heroicons/react/24/outline";

const MobileNavbar = ({ navItems = [] }) => {
  const [isOpen, setIsOpen] = useState(false);
  const toggleMenu = () => setIsOpen((prevState) => !prevState);

  return (
    <div className="sm:hidden ">
      <div
        className="rounded-full p-2 cursor-pointer bg-base-300 shadow-xl"
        onClick={toggleMenu}
      >
        {isOpen ? (
          <XMarkIcon className="w-4 h-4" />
        ) : (
          <Bars4Icon className="w-4 h-4" />
        )}
      </div>
      <div
        className={`fixed inset-0 bg-black bg-opacity-50 transition-opacity duration-300 z-40 ${
          isOpen ? "opacity-100" : "opacity-0 pointer-events-none"
        }`}
      >
        <div
          className={`fixed bottom-0 left-0 right-0 relative transform transition-transform duration-300 z-50 ${
            isOpen ? "translate-y-0" : "translate-y-full"
          } bg-base-100 shadow-lg`}
        >
          <button
            className="absolute right-10 bg-black text-white font-bold top-10 shadow-md rounded-full p-2 cursor-pointer"
            onClick={toggleMenu}
          >
            <XMarkIcon className="w-6 h-6" />
          </button>
          <div className="p-5 flex justify-center items-center min-h-screen">
            <ul className="text-center space-y-10">
              {navItems.map((item) => (
                <li
                  key={item.text}
                  className="text-xl hover:scale-105"
                  onClick={() => setIsOpen(false)}
                >
                  <Link to={item.href}>{item.text}</Link>
                </li>
              ))}
            </ul>
          </div>
        </div>
      </div>
    </div>
  );
};

export default MobileNavbar;
  ```
  