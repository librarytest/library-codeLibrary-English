---
  title: 'CodeDownload'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # CodeDownload.jsx
  ## Code Explanation

This code snippet is a React component named `CodeDownload` that handles the extraction and download of code blocks from a markdown file. Let's break down the key components and functionalities of this code:

1. **Dependencies**:
   - `DocumentTextIcon` from `@heroicons/react/24/outline`: This library provides icons for the UI.
   - `useLocalization` from `"../../../context/LocalizationContext"`: Custom hook to handle localization settings.

2. **Functionality**:
   - The `CodeDownload` component takes a prop `selectedFileContent`, which is the content of the selected markdown file.
   - The `extractCodeBlocks` function is used to extract code blocks from the markdown content. It looks for a specific title and code blocks under the "## Component Code" section.
   - The `handleDownloadCodeFiles` function triggers the download of the extracted code as a text file. It creates a Blob object, generates a download link, and initiates the download process.

3. **Code Extraction**:
   - The `extractCodeBlocks` function uses regular expressions to find the title and code blocks within the markdown content.
   - It cleans up the code blocks by removing unnecessary characters and replaces triple backticks with `///`.

4. **Download Process**:
   - When the user clicks on the download button, the `handleDownloadCodeFiles` function is called.
   - It checks if a markdown file is selected and then extracts the code blocks using `extractCodeBlocks`.
   - If no code blocks are found, it displays an alert. Otherwise, it creates a Blob with the code content, generates a download link, and triggers the download.

5. **UI**:
   - The component renders a hidden download link (`<a id="download-code-link" style={{ display: 'none' }}></a>`) and a visible button for downloading code.
   - The button text changes based on the language setting (`isSpanish`).

## Example Usage

```jsx
import CodeDownload from './CodeDownload';

const App = () => {
  const selectedFileContent = `...`; // Markdown content of the selected file

  return (
    <div>
      <CodeDownload selectedFileContent={selectedFileContent} />
    </div>
  );
};
```

In this example, the `CodeDownload` component is used within an `App` component to handle the extraction and download of code blocks from a selected markdown file.
  
  ## Component Code
  ```jsx
  import { DocumentTextIcon } from "@heroicons/react/24/outline";
import { useLocalization } from "../../../context/LocalizationContext";
const CodeDownload = ({ selectedFileContent }) => {
  const {isSpanish} = useLocalization()
  const extractCodeBlocks = (markdownContent) => {
    const titleMatch = markdownContent.match(/title:\s*'([^']+)'/);
    const title = titleMatch ? titleMatch[1] : "code";

    const componentCodeStart = markdownContent.indexOf("## Component Code");
    if (componentCodeStart === -1) return { title, code: "" };

    const codeSection = markdownContent.slice(componentCodeStart);
    const codeBlockRegex = /```[\s\S]*?```/g;
    const codeBlocks = codeSection.match(codeBlockRegex) || [];

    const cleanedCodeBlocks = codeBlocks.map(block => {
      // Remove the ``` and language identifier
      const cleanedBlock = block.replace(/```[a-z]*\n([\s\S]*?)\n```/, '$1').trim();
      // Replace any remaining ``` with ///
      return cleanedBlock.replace(/```/g, '///');
    }).join('\n\n');

    return {
      title,
      code: cleanedCodeBlocks
    };
  };

  const handleDownloadCodeFiles = () => {
    if (selectedFileContent) {
      const { title, code } = extractCodeBlocks(selectedFileContent);
      if (!code) {
        alert("No code blocks found after '## Component Code'.");
        return;
      }
      const blob = new Blob([code], { type: 'text/plain' });
      const url = window.URL.createObjectURL(blob);

      const downloadLink = document.getElementById('download-code-link');
      downloadLink.href = url;
      downloadLink.download = title;
      downloadLink.click();
      window.URL.revokeObjectURL(url);
    } else {
      alert("Please select a markdown file to download code from.");
    }
  };

  return (
    <>
      <a id="download-code-link" style={{ display: 'none' }}></a>
      <a
        className="btn btn-primary flex justify-center rounded-2xl cursor-pointer"
        onClick={handleDownloadCodeFiles}
      >
        <DocumentTextIcon className="w-8" />
        <span>{isSpanish ? "Descargar Codigo":"Download Code"}</span>
      </a>
    </>
  );
};

export default CodeDownload;
  ```
  