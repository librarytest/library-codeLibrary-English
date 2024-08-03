---
  title: 'PopUp'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # PopUp.jsx
  # Explanation of Popup Component

The provided code is a React component named `Popup` that creates a modal popup dialog box. Let's break down the code and understand its functionality:

### Dependencies
- **React**: The code uses React for building user interfaces.
- **Framer Motion**: It is used for animations in React components.
- **Heroicons React**: Specifically, the `XMarkIcon` component from Heroicons React is used for displaying a close icon.

### Component Props
1. `popupId`: Unique identifier for the popup dialog.
2. `title`: Title of the popup dialog.
3. `description`: Description or content of the popup dialog.
4. `isOpen`: Boolean flag to control the visibility of the popup.
5. `onClose`: Function to be called when the popup is closed.
6. `isLoading`: Boolean flag indicating if content is loading.
7. `isSuccess`: Boolean flag indicating if the operation was successful.
8. `isError`: Boolean flag indicating if an error occurred.
9. `successMessage`: Message to display on success.
10. `errorMessage`: Message to display on error.
11. `children`: Content to be displayed within the popup.
12. `customStyle`: Custom CSS classes for styling the popup.
13. `onSuccess`: Function to be called on successful completion.
14. `progress`: Progress percentage for loading indication.

### State
- `showMessage`: State variable to control the display of success or error messages.

### useEffect
1. The first `useEffect` hook manages the opening and closing of the dialog based on the `isOpen` prop. It adds an event listener for the dialog close event.
2. The second `useEffect` hook handles showing and hiding messages based on loading, success, or error states. It also triggers the `onSuccess` function on successful completion.

### Render
- The component renders a dialog element with the provided `popupId`.
- It contains different sections based on loading, success/error messages, and the main content.
- The close button triggers the `onClose` function.

### Example Usage
```jsx
<Popup
  popupId="myPopup"
  title="Welcome"
  description="This is a sample popup"
  isOpen={true}
  onClose={() => setIsOpen(false)}
  isLoading={false}
  isSuccess={true}
  isError={false}
  successMessage="Operation successful!"
  errorMessage="An error occurred"
  customStyle="custom-popup"
  onSuccess={() => console.log("Success callback")}
  progress={50}
>
  {/* Content to be displayed in the popup */}
</Popup>
```

In this example, the `Popup` component is rendered with various props to control its behavior and appearance.

This explanation provides an overview of the `Popup` component and how it can be used in a React application to display modal dialogs with loading, success, and error states.
  
  ## Component Code
  ```jsx
  import { useState, useEffect } from "react";
import { motion, AnimatePresence } from "framer-motion";
import { XMarkIcon } from "@heroicons/react/24/outline";

const Popup = ({
  popupId,
  title,
  description,
  isOpen,
  onClose,
  isLoading,
  isSuccess,
  isError,
  successMessage,
  errorMessage,
  children,
  customStyle = "modal-box",
  onSuccess,
  progress,
}) => {
  const [showMessage, setShowMessage] = useState(false);

  useEffect(() => {
    const dialog = document.getElementById(popupId);

    if (isOpen) {
      dialog.showModal();
    } else {
      dialog.close();
    }

    const handleClose = () => {
      if (onClose) {
        onClose();
      }
    };

    dialog.addEventListener("close", handleClose);

    return () => {
      dialog.removeEventListener("close", handleClose);
    };
  }, [popupId, isOpen, onClose]);

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
    <dialog id={popupId} className="modal">
      <div
        className={`relative bg-base-200 border border-2 border-black shadow-2xl shadow-white/40 ${customStyle}`}
      >
        <div className="text-center my-10 space-y-1">
          <h2 className="font-bold text-3xl">{title}</h2>
          <p className="text-base-content/70 text-sm">{description}</p>
        </div>
        <div className="relative">
          <AnimatePresence>
            {isLoading && (
              <motion.div
                key="loading"
                initial={{ opacity: 0, x: "-100%" }}
                animate={{ opacity: 1, x: 0 }}
                exit={{ opacity: 0, x: "100%" }}
                transition={{ duration: 0.5 }}
                className=" inset-0 flex flex-col items-center justify-center z-10 "
              >
                <div className="flex">
                  <span className="loading loading-dots loading-lg"></span>
                  <span className="loading loading-dots loading-lg"></span>
                  <span className="loading loading-dots loading-lg"></span>
                  <span className="loading loading-dots loading-lg"></span>
                </div>

                {progress !== undefined && (
                  <div className="flex justify-center">
                    <p className="bg-gray-800 text-white text-[150px] px-3 py-1 rounded-md">
                      {Math.round(progress)}%
                    </p>
                  </div>
                )}
              </motion.div>
            )}
          </AnimatePresence>
          <AnimatePresence>
            {showMessage && (
              <motion.div
                key="message"
                initial={{ opacity: 0, x: "-100%" }}
                animate={{ opacity: 1, x: 0 }}
                exit={{ opacity: 0, x: "100%" }}
                transition={{ duration: 0.5 }}
                className="absolute inset-0 flex items-center justify-center bg-opacity-75 z-10"
              >
                <div
                  className={`message ${
                    isSuccess ? "text-green-500" : "text-red-500"
                  }`}
                >
                  {isSuccess ? successMessage : errorMessage}
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
        </div>
        <button
          type="button"
          className="absolute top-5 right-10"
          onClick={onClose}
        >
          <XMarkIcon className="w-6" />
        </button>
      </div>
    </dialog>
  );
};

export default Popup;
  ```
  