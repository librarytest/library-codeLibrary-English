---
  title: 'index'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # index.jsx
  # Code Explanation

The provided code is a React component named `CustomizePrompt`. It imports various components and hooks to customize a prompt form and display a Markdown preview.

### Components and Hooks Used:
1. **MarkDownPreview**: A component used to display a preview of Markdown content.
2. **PromptForm**: A custom form component for handling prompts.
3. **Popup**: A UI component for displaying pop-up messages.
4. **usePromptForm**: A custom hook that manages the state and logic for the prompt form.

### Functions and Variables:
- **useState**: The `useState` hook is used to manage various states like `formState`, `markdownContent`, `promptExamples`, `isLoading`, `isSuccess`, `isError`, `isModalOpen`, and `currentAction`.
- **useEffect**: This hook might be used inside the `usePromptForm` hook to handle side effects like fetching data or updating the state.
- **handleClose**: A function that closes the modal and navigates back if the current action is saving instructions and there are no errors.

### Usage:
1. The `CustomizePrompt` component renders different UI elements based on screen size.
2. It displays a pop-up modal (`Popup`) for processing messages and success/error notifications.
3. The `PromptForm` component is used to handle form inputs, file changes, example clicks, running tests, saving instructions, and navigation.
4. The Markdown content is displayed using the `MarkDownPreview` component if available.
5. The component also shows a message for mobile users indicating that the feature is intended for laptops.

### Example Usage:
```jsx
import React from 'react';
import CustomizePrompt from './CustomizePrompt';

const App = () => {
  return (
    <div>
      <h1>Customize Your Prompt</h1>
      <CustomizePrompt />
    </div>
  );
};

export default App;
```

In this example, the `CustomizePrompt` component is used within an `App` component to allow users to customize prompts.
  
  ## Component Code
  ```jsx
  import MarkDownPreview from "../../ui/MarkDownPreview";
import PromptForm from "./PromptForm";
import Popup from "../../ui/PopUp";
import usePromptForm from "./hooks/usePromptForm";

const CustomizePrompt = () => {
  const {
    formState,
    markdownContent,
    promptExamples,
    isLoading,
    isSuccess,
    isError,
    isModalOpen,
    setIsModalOpen,
    handleInputChange,
    handleFileChange,
    handleExampleClick,
    handleRunTest,
    handleSaveInstructions,
    currentAction,
    navigate,
  } = usePromptForm();

  const handleClose = () => {
    setIsModalOpen(false);
    if (currentAction === "saveInstructions" && !isError) {
      navigate(-1);
    }
  };

  return (
    <>
    <div className=" bg-orange-100 h-screen hidden lg:flex">
      <Popup
        popupId="loading_modal"
        isOpen={isModalOpen}
        onClose={handleClose}
        title="Processing"
        description="Please wait while we process your request."
        isLoading={isLoading}
        isSuccess={isSuccess}
        isError={isError}
        successMessage="Operation completed successfully!"
        errorMessage="An error occurred."
        customStyle="w-[500px] p-5 rounded-2xl"
        onSuccess={handleClose}
      />

      <PromptForm
        formState={formState}
        handleInputChange={handleInputChange}
        handleFileChange={handleFileChange}
        handleExampleClick={handleExampleClick}
        promptExamples={promptExamples}
        handleRunTest={handleRunTest}
        handleSaveInstructions={handleSaveInstructions}
        navigate={navigate}
      />

      <div className="flex flex-1 h-screen overflow-y-scroll px-20">
        {markdownContent && <MarkDownPreview selectedFileContent={markdownContent} />}
      </div>
    </div>
    <div className="text-center flex items-center justify-center lg:hidden h-screen p-8 bg-orange-100">
          <p className="text-xl">
            Oh no this feature was intended to be use on your Laptop{" "}
          </p>
        </div>
    </>
  );
};

export default CustomizePrompt;
  ```
  