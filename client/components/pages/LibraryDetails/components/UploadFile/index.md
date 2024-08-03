---
  title: 'index'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # index.jsx
  # Upload File Component Explanation

The provided code is a React functional component named `UploadFile`. It handles the functionality related to uploading files to a repository. Let's break down the code and explain its key parts:

### Dependencies
- **React**: The code uses React for building user interfaces.
- **@heroicons/react**: This library provides icons for the UI components.
- **useLoadingIndicator**: A custom hook for managing loading indicators.
- **Popup**: A custom UI component for displaying pop-up messages.
- **useLibrary, useInstructions, useMainLibrary, useLocalization**: Custom context hooks for accessing library, user instructions, main library, and localization data.

### State Variables
1. **selectedFiles**: Stores the files selected by the user for upload.
2. **newDirectory**: Represents the new directory name for file upload.
3. **error**: Holds any error message that occurs during file upload.
4. **selectedInstruction**: Stores the selected instruction for the file upload.
5. **progress**: Tracks the progress of file upload.

### Text Constants
- **englishText** and **spanishText**: Objects containing text messages in English and Spanish for UI elements.

### useEffect Hooks
1. **useEffect 1**: Updates the upload path based on the new directory or existing upload path.
2. **useEffect 2**: Establishes a WebSocket connection for real-time upload progress updates.

### Functions
1. **handleFileUpload**: Handles the file upload process by sending a POST request to the server with file data and additional information.
2. **handleSubmit**: Triggered on form submission, initiates the file upload process after handling loading indicators.
3. **handleModalClose**: Resets state variables and closes the upload modal.
4. **handleClearFiles**: Clears the selected files and any error messages.

### Rendered Components
- **Button**: Triggers the opening of the upload modal.
- **Popup Component**: Displays the upload modal with file upload form, progress bar, and error messages.
- **UploadForm Component**: Contains the form for selecting files, input fields, and instructions for file upload.
- **DirView Component**: Renders the directory view for selecting the upload path.

### Example Usage
```jsx
import React from "react";
import UploadFile from "./components/UploadFile";

const App = () => {
  return (
    <div>
      <h1>File Upload App</h1>
      <UploadFile />
    </div>
  );
};

export default App;
```

This component encapsulates the logic for uploading files, handling user interactions, and displaying relevant messages during the upload process.
  
  ## Component Code
  ```jsx
  import { useState, useEffect } from "react";
import { ArrowUpTrayIcon } from "@heroicons/react/24/outline";
import useLoadingIndicator from "../../../../hooks/useLoadingIndicator";
import UploadForm from "./UploadForm";
import DirView from "./DirView";
import { useLibrary } from "../../context/LibraryContext";
import { useInstructions } from "../../../../context/UserInstructions";
import { useMainLibrary } from "../../../../context/MainLibraryContext";
import Popup from "../../../../ui/PopUp";
import { useLocalization } from "../../../../context/LocalizationContext";

const englishText = {
  addNewFile: "Add New File",
  uploadNewFile: "Upload New File",
  selectFile: "Select a file to upload to the repository.",
  successMessage: "File uploaded successfully!",
  errorMessage: "Failed to upload file.",
};

const spanishText = {
  addNewFile: "Subir Documentos",
  uploadNewFile: "Subir Nuevo Archivo",
  selectFile: "Seleccione un archivo para subir al repositorio.",
  successMessage: "¡Archivo subido con éxito!",
  errorMessage: "Error al subir el archivo.",
};

const UploadFile = () => {
  const { repoName, loadContents, uploadPath, setUploadPath } = useLibrary();
  const { isModalOpen, setIsModalOpen, isLoading, isSuccess, isError, handleLoading } = useLoadingIndicator();
  const [selectedFiles, setSelectedFiles] = useState([]);
  const [newDirectory, setNewDirectory] = useState("");
  const [error, setError] = useState("");
  const [selectedInstruction, setSelectedInstruction] = useState("");
  const { userInstructions } = useInstructions();
  const { userProfile } = useMainLibrary();
  const [progress, setProgress] = useState(0);
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  useEffect(() => {
    setUploadPath(newDirectory ? "" : uploadPath);
  }, [newDirectory, uploadPath, setUploadPath]);

  useEffect(() => {
    let ws;
    if (isModalOpen && userProfile?.id) {
      const wsProtocol = window.location.protocol === "https:" ? "wss:" : "ws:";
      const wsHost = window.location.host;
      const wsUrl = `${wsProtocol}//${wsHost}`;

      ws = new WebSocket(wsUrl, userProfile.id);
      console.log("User ID:", userProfile.id);

      ws.onopen = () => {
        console.log("WebSocket connection established");
      };

      ws.onmessage = (event) => {
        const data = JSON.parse(event.data);
        if (data.progress !== undefined) {
          console.log(`Upload progress: ${data.progress}%`);
          setProgress(data.progress);
        }
      };

      ws.onerror = (error) => {
        console.error("WebSocket error:", error);
      };

      ws.onclose = () => {
        console.log("WebSocket connection closed");
      };
    }

    return () => {
      if (ws) {
        ws.close();
      }
    };
  }, [isModalOpen, userProfile]);

  const handleFileUpload = async () => {
    if (selectedFiles.length === 0 || !(uploadPath || newDirectory)) return;

    const formData = new FormData();
    selectedFiles.forEach((file) => formData.append("files", file));
    formData.append("repository", repoName);
    formData.append("path", uploadPath || newDirectory);

    if (selectedInstruction) {
      const instruction = userInstructions.find((inst) => inst.id === selectedInstruction);
      formData.append("instructionName", instruction.name);
      formData.append("model", instruction.model);
      formData.append("instructions", instruction.instructions);
    }

    try {
      const response = await fetch("/api/upload-file", {
        method: "POST",
        credentials: "include",
        body: formData,
      });

      if (!response.ok) {
        const errorText = await response.text();
        console.error("Error response from server:", errorText);
        throw new Error("Failed to upload file");
      }

      const result = await response.json();
      console.log("File upload response:", result);
    } catch (error) {
      console.error("File upload error:", error);
      setError("Failed to upload file. Please check the console for more details.");
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    handleLoading(handleFileUpload);
  };

  const handleModalClose = () => {
    setSelectedFiles([]);
    setError("");
    setUploadPath("");
    setNewDirectory("");
    setIsModalOpen(false);
    setSelectedInstruction("");
    setProgress(0);
  };

  const handleClearFiles = () => {
    setSelectedFiles([]);
    setError("");
  };

  console.log(progress);

  return (
    <>
      <button className="btn btn-outline btn-secondary rounded-2xl" onClick={() => setIsModalOpen(true)}>
        <ArrowUpTrayIcon className="w-8" />
        {text.addNewFile}
      </button>
      <Popup
        popupId="upload_file_modal"
        title={text.uploadNewFile}
        description={text.selectFile}
        isOpen={isModalOpen}
        onClose={handleModalClose}
        isLoading={isLoading}
        isSuccess={isSuccess}
        isError={isError}
        successMessage={text.successMessage}
        errorMessage={text.errorMessage}
        onSuccess={loadContents}
        customStyle="w-[1050px] h-[90%] p-10 rounded-2xl overflow-y-scroll"
        progress={progress}
      >
        <UploadForm
          selectedFiles={selectedFiles}
          setSelectedFiles={setSelectedFiles}
          setError={setError}
          error={error}
          handleClearFiles={handleClearFiles}
          handleSubmit={handleSubmit}
          isLoading={isLoading}
          newDirectory={newDirectory}
          setNewDirectory={setNewDirectory}
          selectedInstruction={selectedInstruction}
          setSelectedInstruction={setSelectedInstruction}
          dirView={<DirView />}
        />
        {error && <div className="text-red-500 mt-4">{error}</div>}
      </Popup>
    </>
  );
};

export default UploadFile;
  ```
  