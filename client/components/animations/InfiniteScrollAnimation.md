---
  title: 'InfiniteScrollAnimation'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # InfiniteScrollAnimation.jsx
  # Explanation of InfiniteScrollAnimation Component

The `InfiniteScrollAnimation` component is a React component that creates an infinite scroll animation effect for its children elements. It utilizes the `framer-motion` library for animations and motion effects.

### Dependencies
- **React**: The component is built using React, a JavaScript library for building user interfaces.
- **framer-motion**: This library provides components and hooks for creating animations and gestures in React applications.

### Code Explanation
1. **Imports**:
   - `useEffect` and `useRef` are imported from React for managing side effects and creating references.
   - `LazyMotion`, `domAnimation`, `m`, and `useAnimation` are imported from `framer-motion` for animation-related functionalities.

2. **InfiniteScrollAnimation Component**:
   - The component takes three props: `children` (the elements to be animated), `speed` (animation speed, default is 1), and `direction` (animation direction, default is "left").
   - Inside the component, `useAnimation` hook is used to create animation controls, and `useRef` is used to create a reference to the scroll container.

3. **useEffect Hook**:
   - The `useEffect` hook is used to calculate the width of the scroll container and initiate the animation.
   - It sets up the animation based on the direction provided, calculates the initial position, and starts the animation with specified transition properties.
   - It also handles resizing of the window by recalculating the width and re-triggering the animation.

4. **Return Statement**:
   - The component returns a `div` element with the class `overflow-hidden relative` and inline styles for width and maximum width.
   - Inside the `LazyMotion` component, an `m.div` element is used with the reference to the scroll container, animation controls, and styles for flex display and width.
   - The children elements are duplicated within the `m.div` to create the infinite scroll effect.

### Example Usage:
```jsx
import React from "react";
import InfiniteScrollAnimation from "./InfiniteScrollAnimation";

const App = () => {
  return (
    <InfiniteScrollAnimation speed={2} direction="right">
      <div>Element 1</div>
      <div>Element 2</div>
      <div>Element 3</div>
    </InfiniteScrollAnimation>
  );
};

export default App;
```

In this example, the `InfiniteScrollAnimation` component is used to animate three `div` elements with an increased speed of 2 and a rightward direction for the infinite scroll effect.
  
  ## Component Code
  ```jsx
  "use client";
import { useEffect, useRef } from "react";
import { LazyMotion, domAnimation, m, useAnimation } from "framer-motion";

const InfiniteScrollAnimation = ({ children, speed = 1, direction = "left" }) => {
  const controls = useAnimation();
  const scrollContainerRef = useRef(null);

  useEffect(() => {
    const calculateWidthAndAnimate = () => {
      const scrollContainer = scrollContainerRef.current;
      if (scrollContainer) {
        const fullWidth = scrollContainer.scrollWidth / 2; // Adjust based on content duplication
        const initialX = direction === "right" ? -fullWidth : 0;

        controls.start({
          x: direction === "left" ? [-fullWidth, 0] : [0, -fullWidth],
          transition: {
            duration: (fullWidth / 100) * speed,
            ease: "linear",
            repeat: Infinity,
            repeatType: "loop"
          }
        });

        if (direction === "right") {
          // Set initial position when direction is right
          controls.set({ x: initialX });
        }
      }
    };

    calculateWidthAndAnimate();

    const handleResize = () => {
      setTimeout(calculateWidthAndAnimate, 150);
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, [controls, speed, direction]);

  return (
    <div className="overflow-hidden relative" style={{ width: "100%", maxWidth: "100vw" }}>
      <LazyMotion features={domAnimation}>
        <m.div
          ref={scrollContainerRef}
          className="flex items-center gap-4 justify-start"
          animate={controls}
          style={{ display: "flex", minWidth: "200%" }} // Ensure the container is always wider
        >
          {children}
          {children} {/* Duplicate children to create an infinite scroll effect */}
        </m.div>
      </LazyMotion>
    </div>
  );
};

export default InfiniteScrollAnimation;
  ```
  