---
  title: 'useLoadingIndicator.js'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # useLoadingIndicator.js
  # Explanation of `useLoadingIndicator` Custom Hook

The provided code snippet is a custom React hook named `useLoadingIndicator`. This hook is designed to manage loading states, success states, and error states for asynchronous operations, along with handling a loading modal.

### Dependencies
- The code snippet uses React hooks `useState` and `useEffect` from the `react` library.

### Functions and Constructs
1. **useState**: This hook is used to declare state variables in functional components. In this code, it is used to manage the states of `isModalOpen`, `isLoading`, `isSuccess`, and `isError`.

2. **useEffect**: This hook is used to perform side effects in function components. Here, it is used to handle the logic after an operation is successful or encounters an error.

3. **handleLoading**: This function takes a `loadingFunction` as an argument, sets the loading state to true, and then executes the `loadingFunction`. If the function resolves successfully, it sets `isSuccess` to true; otherwise, it sets `isError` to true. Finally, it sets `isLoading` back to false.

### Purpose of States
- `isModalOpen`: Indicates whether a modal is open.
- `isLoading`: Indicates whether an operation is in progress.
- `isSuccess`: Indicates whether the operation was successful.
- `isError`: Indicates whether an error occurred during the operation.

### Usage Example
```jsx
import React from "react";
import useLoadingIndicator from "./useLoadingIndicator";

const MyComponent = () => {
  const {
    isModalOpen,
    setIsModalOpen,
    isLoading,
    isSuccess,
    isError,
    handleLoading,
    setIsError,
    setIsSuccess
  } = useLoadingIndicator();

  const fetchData = async () => {
    // Simulated async operation
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        const success = Math.random() < 0.8; // Simulating success/failure randomly
        success ? resolve("Data fetched successfully!") : reject("Error fetching data!");
      }, 2000);
    });
  };

  const handleClick = () => {
    handleLoading(fetchData);
  };

  return (
    <div>
      {isLoading && <p>Loading...</p>}
      {isSuccess && <p>Operation successful!</p>}
      {isError && <p>Error occurred!</p>}
      <button onClick={handleClick} disabled={isLoading}>
        Trigger Operation
      </button>
      {isModalOpen && <Modal />}
    </div>
  );
};

export default MyComponent;
```

In the example usage above, `MyComponent` uses the `useLoadingIndicator` custom hook to manage loading states for an asynchronous operation triggered by a button click. The hook handles showing loading, success, and error messages, along with managing a modal that can be closed on successful operation completion.
  
  ## Component Code
  ```jsx
  import { useState, useEffect } from "react";

const useLoadingIndicator = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [isSuccess, setIsSuccess] = useState(false);
  const [isError, setIsError] = useState(false);

  const handleLoading = async (loadingFunction) => {
    setIsLoading(true);
    setIsSuccess(false);
    setIsError(false);

    try {
      await loadingFunction();
      setIsSuccess(true);
    } catch (error) {
      setIsError(true);
    } finally {
      setIsLoading(false);
    }
  };

  useEffect(() => {
    if (isSuccess || isError) {
      const timer = setTimeout(() => {
        if (isSuccess) {
          setIsModalOpen(false); // Close the modal on success
        }
        setIsSuccess(false);
        setIsError(false);
      }, 2000);
      return () => clearTimeout(timer);
    }
  }, [isSuccess, isError]);

  return {
    isModalOpen,
    setIsModalOpen,
    isLoading,
    isSuccess,
    isError,
    handleLoading,
    setIsError,
    setIsSuccess
  };
};

export default useLoadingIndicator;
  ```
  