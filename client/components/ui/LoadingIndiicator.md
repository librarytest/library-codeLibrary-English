---
  title: 'LoadingIndiicator'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # LoadingIndiicator.jsx
  # Loading Indicator Component Explanation

The provided code is a React functional component called `LoadingIndicator` that displays a loading indicator, success message, or error message based on the props passed to it. The component utilizes the `useState` and `useEffect` hooks from React and animations from the `framer-motion` library.

### Code Explanation
- **useState**: The `useState` hook is used to manage the state of `showMessage`, which controls whether to display the success or error message.
- **useEffect**: The `useEffect` hook is used to handle side effects like showing/hiding messages based on the props passed to the component.
- **motion**: The `motion` component from `framer-motion` is used for animations within the component.
- **AnimatePresence**: The `AnimatePresence` component from `framer-motion` is used to handle the presence of components during animations.

### Component Props
- **isLoading**: A boolean flag indicating if the component is in a loading state.
- **isSuccess**: A boolean flag indicating if the operation was successful.
- **isError**: A boolean flag indicating if an error occurred.
- **successMessage**: The message to display on success.
- **errorMessage**: The message to display on error.
- **children**: The content to display when not in a loading state.
- **onSuccess**: A callback function to execute on successful completion.

### Component Behavior
- When `isLoading` is true, the loading indicator is displayed.
- When `isSuccess` or `isError` is true, the success or error message is displayed respectively for 2 seconds before hiding.
- If `isSuccess` is true and `onSuccess` is provided, the `onSuccess` callback is executed after the success message is displayed.

### Example Usage
```jsx
<LoadingIndicator
  isLoading={loading}
  isSuccess={success}
  isError={error}
  successMessage="Operation successful!"
  errorMessage="An error occurred."
  onSuccess={handleSuccess}
>
  {/* Your content goes here */}
</LoadingIndicator>
```

In the example usage above, the `LoadingIndicator` component is used with props to control its behavior based on the loading, success, and error states. The `handleSuccess` function will be called on successful completion.
  
  ## Component Code
  ```jsx
  import { useState, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';


const LoadingIndicator = ({ isLoading, isSuccess, isError, successMessage, errorMessage, children, onSuccess }) => {
  const [showMessage, setShowMessage] = useState(false);

  useEffect(() => {
    if (isLoading) {
      setShowMessage(false);
    } else if (isSuccess || isError) {
      setShowMessage(true);
      const timer = setTimeout(() => {
        setShowMessage(false);
        if (isSuccess && onSuccess) {
          onSuccess();
        }
      }, 2000);
      return () => clearTimeout(timer);
    }
  }, [isLoading, isSuccess, isError, onSuccess]);

  return (
    <div className="relative">
      <AnimatePresence>
        {isLoading && (
          <motion.div
            key="loading"
            initial={{ opacity: 0, x: '-100%' }}
            animate={{ opacity: 1, x: 0 }}
            exit={{ opacity: 0, x: '100%' }}
            transition={{ duration: 0.5 }}
            className="absolute inset-0 flex items-center justify-center bg-white bg-opacity-75 z-10"
          >
            <div className='flex'>
              <span className="loading loading-dots loading-lg"></span>
              <span className="loading loading-dots loading-lg"></span>
              <span className="loading loading-dots loading-lg"></span>
              <span className="loading loading-dots loading-lg"></span>
            </div>
          </motion.div>
        )}
      </AnimatePresence>
      {!isLoading && !showMessage && (
        <motion.div
          key="form"
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          transition={{ duration: 0.5 }}
          className="relative"
        >
          {children}
        </motion.div>
      )}
      <AnimatePresence>
        {showMessage && (
          <motion.div
            key="message"
            initial={{ scale: 0.5, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            exit={{ scale: 0.5, opacity: 0 }}
            transition={{ duration: 0.5 }}
            className="absolute inset-0 flex items-center justify-center bg-white bg-opacity-75 z-10"
          >
            <div className={`message ${isSuccess ? 'text-green-500' : 'text-red-500'}`}>
              {isSuccess ? successMessage : errorMessage}
            </div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
};

export default LoadingIndicator;
  ```
  