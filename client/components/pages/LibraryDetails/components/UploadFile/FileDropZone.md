---
  title: 'FileDropZone'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # FileDropZone.jsx
  # FileDropZone Component Explanation

The provided code is a React component called `FileDropZone` that allows users to drag and drop files or select files from their system. Below is a detailed explanation of the code:

### Libraries and Dependencies
- The code uses the `@heroicons/react` library to import icons like `DocumentTextIcon` and `ArrowUpTrayIcon`.
- The `useLocalization` function is imported from a custom context named `LocalizationContext`.

### Component Logic
1. Two objects `englishText` and `spanishText` are defined to hold text messages in English and Spanish, respectively.
2. The component `FileDropZone` takes three props: `selectedFiles`, `setSelectedFiles`, and `setError`.
3. The component uses the `useLocalization` hook to determine the language preference (English or Spanish).
4. An array `allowedExtensions` contains a list of file extensions that are allowed to be uploaded.
5. The maximum number of files that can be uploaded is set to 4.
6. The `handleDragOver` function prevents the default behavior during a drag event.
7. The `handleDrop` function is triggered when files are dropped into the drop zone. It extracts the files and validates them.
8. The `extractFiles` function processes the dropped items and extracts valid files.
9. The `traverseFileTree` function recursively traverses a directory to extract files.
10. The `isValidFile` function checks if a file has a valid extension based on the `allowedExtensions` array.
11. The `handleFileSelect` function is triggered when files are selected using the file input. It validates and sets the selected files.
12. The `validateAndSetFiles` function filters out invalid files and updates the selected files state accordingly.

### Component UI
1. The component renders a `div` element that serves as the drop zone for files.
2. An `input` element of type file is hidden and is triggered when a file is selected.
3. The label is associated with the hidden file input.
4. If files are selected, their icons and names are displayed in a grid layout.
5. If no files are selected, an upload icon and a message are displayed.

### Example Usage
```jsx
import React, { useState } from 'react';
import FileDropZone from './FileDropZone';

const MyComponent = () => {
  const [selectedFiles, setSelectedFiles] = useState([]);
  const [error, setError] = useState('');

  return (
    <div>
      <FileDropZone selectedFiles={selectedFiles} setSelectedFiles={setSelectedFiles} setError={setError} />
      {error && <p>{error}</p>}
    </div>
  );
};

export default MyComponent;
```

In the example usage, `MyComponent` renders the `FileDropZone` component and displays any error messages if file validation fails.

This explanation provides an overview of how the `FileDropZone` component works, including its functionality and usage within a React application.
  
  ## Component Code
  ```jsx
  import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/react/24/outline";
import { useLocalization } from "../../../../context/LocalizationContext";

const englishText = {
  invalidFileType: "Invalid file type. Only the allowed file types are permitted.",
  maxFiles: (max) => `You can only upload a maximum of ${max} files.`,
  dragDrop: "Drag & drop files or folders here, or click to select files",
};

const spanishText = {
  invalidFileType: "Tipo de archivo no válido. Solo se permiten los tipos de archivos permitidos.",
  maxFiles: (max) => `Solo puede subir un máximo de ${max} archivos.`,
  dragDrop: "Arrastre y suelte archivos o carpetas aquí, o haga clic para seleccionar archivos",
};

const FileDropZone = ({ selectedFiles, setSelectedFiles, setError }) => {
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const allowedExtensions = [
    ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
    ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt", 
    ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml", 
    ".vue", ".svelte", ".qwik", ".astro", ".mjs"
  ];
  const maxFiles = 4;

  const handleDragOver = (e) => e.preventDefault();

  const handleDrop = async (e) => {
    e.preventDefault();
    const items = e.dataTransfer.items;
    const filesArray = await extractFiles(items);
    validateAndSetFiles(filesArray);
  };

  const extractFiles = async (items) => {
    const filesArray = [];
    for (const item of items) {
      const entry = item.webkitGetAsEntry();
      if (entry.isFile) {
        const file = await new Promise((resolve) => entry.file(resolve));
        if (isValidFile(file)) {
          filesArray.push(file);
        } else {
          setError(text.invalidFileType);
        }
      } else if (entry.isDirectory) {
        await traverseFileTree(entry, filesArray);
      }
    }
    return filesArray;
  };

  const traverseFileTree = (item, filesArray) => {
    return new Promise((resolve) => {
      if (item.isFile) {
        item.file((file) => {
          if (isValidFile(file)) {
            filesArray.push(file);
          }
          resolve();
        });
      } else if (item.isDirectory) {
        const dirReader = item.createReader();
        dirReader.readEntries(async (entries) => {
          for (const entry of entries) {
            await traverseFileTree(entry, filesArray);
          }
          resolve();
        });
      }
    });
  };

  const isValidFile = (file) => allowedExtensions.some((ext) => file.name.endsWith(ext));

  const handleFileSelect = (e) => {
    const files = Array.from(e.target.files);
    validateAndSetFiles(files);
  };

  const validateAndSetFiles = (files) => {
    const filteredFiles = files.filter(isValidFile);
    if (filteredFiles.length + selectedFiles.length > maxFiles) {
      setError(text.maxFiles(maxFiles));
    } else {
      setSelectedFiles((prev) => [...prev, ...filteredFiles].slice(0, maxFiles));
      setError("");
    }
  };

  return (
    <div
      onDragOver={handleDragOver}
      onDrop={handleDrop}
      className="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-[400px] w-[400px] flex overflow-y-scroll items-center justify-center"
    >
      <input
        type="file"
        onChange={handleFileSelect}
        style={{ display: "none" }}
        accept={allowedExtensions.join(",")}
        id="fileUpload"
        multiple
        required
      />
      <label htmlFor="fileUpload" className="cursor-pointer">
        {selectedFiles.length > 0 ? (
          <div className="flex flex-wrap gap-10">
            {selectedFiles.map((file) => (
              <div key={file.name}>
                <DocumentTextIcon className="mx-auto w-8" />
                <p>{file.name}</p>
              </div>
            ))}
          </div>
        ) : (
          <div>
            <ArrowUpTrayIcon className="mx-auto w-8" />
            <p>{text.dragDrop}</p>
          </div>
        )}
      </label>
    </div>
  );
};

export default FileDropZone;
  ```
  