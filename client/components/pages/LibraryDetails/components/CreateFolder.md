---
  title: 'CreateFolder'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # CreateFolder.jsx
  # Explanation of CreateFolder Component

The `CreateFolder` component is a React functional component that allows users to create a new folder within a specified repository. Below is a detailed explanation of the code:

### Dependencies
- **React**: The component is built using React, a JavaScript library for building user interfaces.
- **@heroicons/react**: This library provides icons for the UI, specifically the `FolderPlusIcon` used in the component.
- **useLoadingIndicator**: A custom hook (`useLoadingIndicator`) is used to manage loading states and indicators.
- **useLibrary**: A context hook (`useLibrary`) is used to access library-related data and functions.
- **useLocalization**: Another context hook (`useLocalization`) is used to determine the language preference for text localization.
- **Popup**: A custom UI component (`Popup`) is used to display a modal for creating a new folder.

### Variables
- **englishText**: An object containing English text strings used in the component.
- **spanishText**: An object containing Spanish text strings used in the component.

### Component Functionality
1. **State Management**:
   - `selectedStructure`: A state variable managed using the `useState` hook to store the selected folder structure.
  
2. **Event Handlers**:
   - `handleCreateFolder`: An asynchronous function that sends a POST request to create a new folder using the selected structure and repository name.
   - `handleSubmit`: A function that handles form submission by triggering the `handleCreateFolder` function.
   - `handleStructureSelect`: A function that updates the `selectedStructure` state based on the user's selection.
   - `handleClose`: A function to close the modal and reset the selected structure.

3. **Rendering**:
   - The component renders a button with the `FolderPlusIcon` and text for adding a new folder.
   - It also renders a `Popup` modal that includes a form for selecting a folder structure and creating a folder.
   - The modal displays loading, success, or error messages based on the API response.
   - The form submission triggers the `handleCreateFolder` function and updates the UI accordingly.

### Example Usage
```jsx
import CreateFolder from './CreateFolder';

const App = () => {
  return (
    <div>
      <h1>Create a New Folder</h1>
      <CreateFolder />
    </div>
  );
};

export default App;
```

In this example, the `CreateFolder` component is used within an `App` component to allow users to create a new folder within a repository. When the button is clicked, a modal pops up for selecting a folder structure and creating the folder.
  
  ## Component Code
  ```jsx
  import { useState } from "react";
import { FolderPlusIcon } from "@heroicons/react/24/outline";
import useLoadingIndicator from "../../../hooks/useLoadingIndicator";
import { useLibrary } from "../context/LibraryContext";
import DirStructureOptions from "./DirStructureOptions";
import Popup from "../../../ui/PopUp";
import { useLocalization } from "../../../context/LocalizationContext";

const englishText = {
  addStructure: "Add Structure",
  addNewFolder: "Add New Folder",
  selectStructure: "Select a folder structure to add.",
  creating: "Creating...",
  createFolder: "Create Folder",
  successMessage: "Folder created successfully!",
  errorMessage: "Failed to create folder.",
};

const spanishText = {
  addStructure: "Agregar Estructura",
  addNewFolder: "Agregar Nueva Carpeta",
  selectStructure: "Seleccione una estructura de carpeta para agregar.",
  creating: "Creando...",
  createFolder: "Crear Carpeta",
  successMessage: "¡Carpeta creada con éxito!",
  errorMessage: "Error al crear la carpeta.",
};

const CreateFolder = () => {
  const { repoName, loadContents } = useLibrary();
  const { isModalOpen, setIsModalOpen, isLoading, isSuccess, isError, handleLoading } = useLoadingIndicator();
  const [selectedStructure, setSelectedStructure] = useState(null);
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const handleCreateFolder = async () => {
    const response = await fetch("/api/create-folder", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      credentials: "include",
      body: JSON.stringify({
        structure: selectedStructure,
        repository: repoName,
      }),
    });

    if (response.ok) {
      console.log(response);
    } else {
      console.error("Failed to create folder");
      throw new Error("Failed to create folder");
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    handleLoading(handleCreateFolder);
  };

  const handleStructureSelect = (structure) => {
    setSelectedStructure(structure);
  };

  const handleClose = () => {
    setIsModalOpen(false);
    setSelectedStructure(null);
  };

  return (
    <>
      <button
        className="btn btn-outline btn-neutral rounded-2xl"
        onClick={() => setIsModalOpen(true)}
      >
        <FolderPlusIcon className="w-8" />
        {text.addStructure}
      </button>
      <Popup
        popupId="create_folder_modal"
        title={text.addNewFolder}
        description={text.selectStructure}
        isOpen={isModalOpen}
        onClose={handleClose}
        isLoading={isLoading}
        isSuccess={isSuccess}
        isError={isError}
        successMessage={text.successMessage}
        errorMessage={text.errorMessage}
        onSuccess={loadContents}
        customStyle="w-[500px] p-5 rounded-2xl"
      >
        <form onSubmit={handleSubmit} className="space-y-4 ">
          <DirStructureOptions onSelect={handleStructureSelect} selectedStructure={selectedStructure} />
          <button
            type="submit"
            disabled={isLoading || !selectedStructure}
            className="btn btn-primary mt-4"
          >
            {isLoading ? text.creating : text.createFolder}
          </button>
        </form>
      </Popup>
    </>
  );
};

export default CreateFolder;
  ```
  