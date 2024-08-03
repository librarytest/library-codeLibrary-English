---
  title: 'CreateFolder'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # CreateFolder.jsx
  # Explanation of CreateFolder Component

The `CreateFolder` component is a React functional component that allows users to create a new folder within a specified repository. Let's break down the code and understand its functionality:

### Dependencies
- **React**: The core library for building user interfaces in React.
- **@heroicons/react**: Provides a set of icons that can be used in React components.
- **useLoadingIndicator**: A custom hook that manages loading indicators.
- **useLibrary**: A custom hook that provides access to library-related data.
- **DirStructureOptions**: A component that displays options for folder structures.
- **Popup**: A UI component for displaying pop-up messages.
- **useLocalization**: A custom hook for managing localization settings.

### Variables
- `englishText` and `spanishText`: Objects containing text in English and Spanish for localization.
- `CreateFolder`: The main functional component.

### State Variables
- `selectedStructure`: State variable to store the selected folder structure.
- `text`: Determines the text based on the selected language (English or Spanish).

### Functions
1. `handleCreateFolder`: Sends a POST request to create a new folder using the selected structure and repository name.
2. `handleSubmit`: Handles form submission by triggering the `handleCreateFolder` function.
3. `handleStructureSelect`: Updates the `selectedStructure` state based on the user's selection.
4. `handleClose`: Closes the modal and resets the selected structure.

### Component Structure
1. Renders a button with an icon and text to trigger the modal for creating a new folder.
2. Displays a pop-up modal with options to select a folder structure and create the folder.
3. The modal includes a form with `DirStructureOptions` for selecting the structure and a button to create the folder.
4. The button is disabled during loading and when no structure is selected.
5. Shows loading, success, or error messages based on the API response.

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

In this example, the `CreateFolder` component is used within an `App` component to allow users to create a new folder in a repository.
  
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
  