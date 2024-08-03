---
  title: 'MovingRectangles'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # MovingRectangles.jsx
  # Explanation of SlidingRectangles Component

The provided code is a React functional component named `SlidingRectangles` that utilizes the Framer Motion library for animations. It creates a visual effect where two rectangles slide into view based on the user's scroll position on the webpage.

### Dependencies
- **React**: A JavaScript library for building user interfaces.
- **Framer Motion**: A library for creating animations in React applications.

### Code Explanation
1. **Imports**:
   - `useEffect` and `useRef` are imported from React to handle side effects and create references.
   - `motion` and `useAnimation` are imported from Framer Motion for animation control.

2. **Component Variables**:
   - `controlsLeft` and `controlsRight` are animation controls created using `useAnimation`.
   - `ref` is a reference to a DOM element created using `useRef`.

3. **handleScroll Function**:
   - This function is responsible for updating the animation based on the user's scroll position.
   - It calculates the visibility of the component based on the scroll position and adjusts the animation properties accordingly.
   - If the scroll position is greater than or equal to 1150, the final positions and opacity of the rectangles are set.
   
4. **useEffect Hook**:
   - Registers event listeners for scroll and resize events when the component mounts.
   - Calls `handleScroll` function on scroll and resize events.
   - Removes event listeners when the component unmounts.

5. **Return Statement**:
   - Returns JSX that contains two `motion.div` elements representing the sliding rectangles.
   - The `animate` prop of each `motion.div` is set to the respective `controlsLeft` and `controlsRight`.
   - The `ref` is attached to the parent `div` to track its visibility in the viewport.

### Example Usage
```jsx
import React from 'react';
import SlidingRectangles from './SlidingRectangles';

const App = () => {
  return (
    <div>
      <h1>Scroll down to see the sliding rectangles</h1>
      <SlidingRectangles />
    </div>
  );
};

export default App;
```

In this example, the `SlidingRectangles` component is imported and rendered within the `App` component. When the user scrolls on the webpage, the sliding rectangles will animate based on their visibility in the viewport.
  
  ## Component Code
  ```jsx
  import  { useEffect, useRef } from 'react';
import { motion, useAnimation } from 'framer-motion';

const SlidingRectangles = () => {
  const controlsLeft = useAnimation();
  const controlsRight = useAnimation();
  const ref = useRef(null);

  const handleScroll = () => {
    const scrollY = window.scrollY;
    if (ref.current) {
      const rect = ref.current.getBoundingClientRect();
      const windowHeight = window.innerHeight;

      // Stop updating if scrollY is greater than or equal to 1150
      if (scrollY >= 1150) {
        // Optional: you might want to set final positions and opacity once when reaching the threshold
        controlsLeft.set({ x: '-0.9573%', opacity: 1 });
        controlsRight.set({ x: '0.9573%', opacity: 1 });
        return;
      }

      if (rect.top < windowHeight && rect.bottom > 0) {
        const visibleHeight = Math.min(windowHeight, rect.bottom) - Math.max(0, rect.top);
        const totalHeight = rect.bottom - rect.top;
        const visibleFraction = Math.max(0, visibleHeight / totalHeight);

        const leftPosition = -100 + visibleFraction * 100;
        const rightPosition = 100 - visibleFraction * 100;

        controlsLeft.set({ x: `${leftPosition}%`, opacity: visibleFraction });
        controlsRight.set({ x: `${rightPosition}%`, opacity: visibleFraction });
      }
    }
  };

  useEffect(() => {
    window.addEventListener('scroll', handleScroll);
    window.addEventListener('resize', handleScroll);

    return () => {
      window.removeEventListener('scroll', handleScroll);
      window.removeEventListener('resize', handleScroll);
    };
  }, []);

  return (
    <div className="relative flex justify-center items-center h-screen overflow-hidden" ref={ref}>
      <motion.div
        animate={controlsLeft}
        className={`absolute mr-[500px]`}
      >
         <img src='code.png'  className='w-[350px] h-[400px]'/>
        </motion.div>
       
      <motion.div
        animate={controlsRight}
        className={`absolute ml-[500px]`}
      >
         <img src='document.png' className='w-[700px] h-[700px]'/>
        </motion.div>
    </div>
  );
};

export default SlidingRectangles;
  ```
  