---
  title: 'UploadForm'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # UploadForm.jsx
  # UploadForm Component Explanation

The `UploadForm` component is a React functional component that handles the UI for uploading files. It utilizes various hooks and context providers to manage state and display content based on user interactions.

### Dependencies
- `FileDropZone`: This component is imported from a local file and is used to handle file dropping functionality.
- `ArrowPathIcon` from `@heroicons/react/24/outline`: An external library providing an arrow icon for UI.
- `useLibrary`, `useInstructions`, `useLocalization`: Custom hooks from different context providers to access library data, user instructions, and localization settings.

### Component Props
- `selectedFiles`: Array of selected files.
- `setSelectedFiles`: Function to update the selected files state.
- `setError`: Function to set an error message.
- `error`: Error message state.
- `handleClearFiles`: Function to clear selected files.
- `handleSubmit`: Function to handle form submission.
- `isLoading`: Boolean state to indicate if the form is in a loading state.
- `newDirectory`: State for the new directory name.
- `setNewDirectory`: Function to update the new directory name state.
- `selectedInstruction`: State for the selected user instruction.
- `setSelectedInstruction`: Function to update the selected user instruction state.
- `dirView`: Component or content to display the selected directory.

### Code Explanation
1. The component conditionally renders text based on the selected language (English or Spanish).
2. The form consists of a file drop zone, input fields for directory path and new directory name, a selection for custom instructions, and buttons for file upload.
3. The `FileDropZone` component handles file selection and updates the selected files state.
4. The `handleClearFiles` function clears the selected files when the clear button is clicked.
5. The input fields for directory path and new directory name allow users to input or view the paths.
6. Custom instructions are displayed as buttons, and users can select one by clicking on it.
7. The submit button is disabled when there is no selected path or new directory name, and it displays different text based on the loading state.
8. A note is displayed at the bottom of the form.
9. The `dirView` component displays the selected directory content.

### Example Usage
```jsx
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
  dirView={dirView}
/>
```

In this example, the `UploadForm` component is rendered with the necessary props passed to it for file uploading functionality.
  
  ## Component Code
  ```jsx
  import FileDropZone from "./FileDropZone";
import { ArrowPathIcon } from "@heroicons/react/24/outline";
import { useLibrary } from "../../context/LibraryContext";
import { useInstructions } from "../../../../context/UserInstructions";
import { useLocalization } from "../../../../context/LocalizationContext";

const englishText = {
  directoryPath: "Directory Path:",
  selectedDirectoryPath: "Selected Directory Path",
  createNewDirectory: "Create New Directory:",
  newDirectoryName: "New Directory Name",
  customInstruction: "Custom Instruction:",
  selectDirectory: "Select a Directory:",
  uploading: "Uploading...",
  uploadFile: "Upload File",
  note: "Note: The default instruction is in English. For other languages, create a custom prompt.",
};

const spanishText = {
  directoryPath: "Ruta del Directorio:",
  selectedDirectoryPath: "Ruta del Directorio Seleccionado",
  createNewDirectory: "Crear Nuevo Directorio:",
  newDirectoryName: "Nombre del Nuevo Directorio",
  customInstruction: "Instrucción Personalizada:",
  selectDirectory: "Seleccione un Directorio:",
  uploading: "Subiendo...",
  uploadFile: "Subir Archivo",
  note: "Nota: La instrucción predeterminada está en inglés. Para otros idiomas, crea un prompt personalizado.",
};

const UploadForm = ({
  selectedFiles,
  setSelectedFiles,
  setError,
  error,
  handleClearFiles,
  handleSubmit,
  isLoading,
  newDirectory,
  setNewDirectory,
  selectedInstruction,
  setSelectedInstruction,
  dirView,
}) => {
  const { uploadPath, setUploadPath } = useLibrary();
  const { userInstructions, loading } = useInstructions();
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  return (
    <form onSubmit={handleSubmit} className="flex gap-10 justify-evenly">
      <div className="relative flex-1">
        <FileDropZone
          selectedFiles={selectedFiles}
          setSelectedFiles={setSelectedFiles}
          setError={setError}
        />
        <button
          type="button"
          onClick={handleClearFiles}
          className="btn bg-transparent top-0 absolute"
        >
          <ArrowPathIcon className="w-6" />
        </button>
      </div>
      <div className="w-[35%]">
        {error && <p className="text-red-500">{error}</p>}

        <div className="mb-4">
          <h3>{text.directoryPath}</h3>
          <input
            type="text"
            value={uploadPath}
            readOnly
            onChange={(e) => setUploadPath(e.target.value)}
            placeholder={text.selectedDirectoryPath}
            className="input w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4"
          />
        </div>

        <div className="mb-4">
          <h3>{text.createNewDirectory}</h3>
          <input
            type="text"
            value={newDirectory}
            onChange={(e) => setNewDirectory(e.target.value)}
            placeholder={text.newDirectoryName}
            className="input w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4"
          />
        </div>

        {!loading && (
          <div className="mb-4 w-full">
            <h3 className="font-semibold">{text.customInstruction}</h3>
            <div className="flex overflow-x-scroll gap-2 w-full mt-4">
              {userInstructions.map((instruction) => (
                <button
                  key={instruction.id}
                  type="button"
                  className={`btn ${
                    selectedInstruction === instruction.id ? "btn-primary" : "btn-secondary"
                  }`}
                  onClick={() => setSelectedInstruction(instruction.id)}
                >
                  {instruction.name}
                </button>
              ))}
            </div>
          </div>
        )}

        <button
          type="submit"
          disabled={isLoading || !(uploadPath || newDirectory)}
          className="btn btn-primary"
        >
          {isLoading ? text.uploading : text.uploadFile}
        </button>

        <p className="mt-4 text-xs">{text.note}</p>
      </div>
      <div className="flex flex-col flex-1 mb-4 overflow-y-scroll">
        <h3 className="font-semibold">{text.selectDirectory}</h3>
        {dirView}
      </div>
    </form>
  );
};

export default UploadForm;
  ```
  