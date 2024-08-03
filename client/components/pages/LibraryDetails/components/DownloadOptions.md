---
  title: 'DownloadOptions'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # DownloadOptions.jsx
  # Explanation of DownloadOptions Component

The provided code is a React component named `DownloadOptions`. This component renders a download options section with two download buttons for markdown files. It also includes functionality to handle downloading a single markdown file or an entire repository.

### External Dependencies
- **Heroicons React**: This code snippet imports the `DocumentTextIcon` component from the Heroicons React library. Heroicons provide a set of icons that can be used in React applications.

### Functions
1. **extractTitle**: This function extracts the title from the markdown content. It uses a regular expression to match the title in the markdown content and returns the title with the extension `.md`.

### Variables
- **englishText**: An object containing English text for various UI elements.
- **spanishText**: An object containing Spanish text for the same UI elements.
- **selectedFileContent**: The content of the selected markdown file.
- **repoName**: The name of the repository.
- **userProfile**: User profile information obtained from the `MainLibraryContext`.
- **isSpanish**: A boolean value indicating whether the current language is set to Spanish.
- **text**: The text object based on the selected language.

### Event Handlers
1. **handleDownloadMarkdown**: This function is called when the user clicks on the "Download MD" button. It extracts the title from the selected markdown content, creates a Blob with the content, generates a URL for the Blob, sets up a download link, and triggers the download.
2. **handleDownloadRepo**: This function is called when the user clicks on the "Download Complete Dir" button. It constructs the URL for downloading the entire repository and opens it in a new tab.

### Usage Example
```jsx
<DownloadOptions selectedFileContent={markdownContent} repoName="myRepo" />
```

In this example, the `DownloadOptions` component is rendered with `markdownContent` as the selected file content and "myRepo" as the repository name. The component will display the download options based on the selected file content and language preference.
  
  ## Component Code
  ```jsx
  import { DocumentTextIcon } from "@heroicons/react/24/outline";
import { useMainLibrary } from "../../../context/MainLibraryContext";
import { useLocalization } from "../../../context/LocalizationContext";

const englishText = {
  downloadMD: "Download MD",
  getOneFile: "Get One File",
  downloadCompleteDir: "Download Complete Dir",
  alertMessage: "Please select a markdown file to download."
};

const spanishText = {
  downloadMD: "Descargar MD",
  getOneFile: "Obtener Documentacion",
  downloadCompleteDir: "Descargar Directorio",
  alertMessage: "Por favor seleccione un archivo markdown para descargar."
};

const DownloadOptions = ({ selectedFileContent, repoName }) => {
  const { userProfile } = useMainLibrary();
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const extractTitle = (markdownContent) => {
    const titleMatch = markdownContent.match(/title:\s*'([^']+)'/);
    return titleMatch ? titleMatch[1].replace(/\.tsx$/, ".md") : "document.md";
  };

  const handleDownloadMarkdown = () => {
    if (selectedFileContent) {
      const title = extractTitle(selectedFileContent);
      const blob = new Blob([selectedFileContent], { type: "text/markdown" });
      const url = window.URL.createObjectURL(blob);

      const downloadLink = document.getElementById("download-md-link");
      downloadLink.href = url;
      downloadLink.download = title;
      downloadLink.click();
      window.URL.revokeObjectURL(url);
    } else {
      alert(text.alertMessage);
    }
  };

  const handleDownloadRepo = () => {
    const repoUrl = `https://github.com/${userProfile.username}/${repoName}/archive/refs/heads/main.zip`;
    window.open(repoUrl, "_blank");
  };

  return (
    <>
      <a id="download-md-link" style={{ display: "none" }}></a>
      <div className="relative group">
        <a
          className={`btn btn-accent flex justify-center rounded-2xl cursor-pointer ${!selectedFileContent && "pointer-events-none"}`}
          onClick={handleDownloadMarkdown}
        >
          <DocumentTextIcon className="w-8" />
          <span>{text.downloadMD}</span>
        </a>
        <div className="absolute hidden group-hover:flex flex-col items-center bg-white border border-black shadow-lg p-2 rounded-lg mb-2 bottom-full pointer-events-auto -mb-3">
          <a
            className="btn btn-outline btn-primary w-full mb-2 cursor-pointer"
            onClick={handleDownloadMarkdown}
          >
            {text.getOneFile}
          </a>
          <button
            className="btn btn-outline btn-primary w-full"
            onClick={handleDownloadRepo}
          >
            {text.downloadCompleteDir}
          </button>
        </div>
      </div>
    </>
  );
};

export default DownloadOptions;
  ```
  