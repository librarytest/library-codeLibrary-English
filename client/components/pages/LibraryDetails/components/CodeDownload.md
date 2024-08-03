---
  title: 'CodeDownload'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # CodeDownload.jsx
  ## Code Explanation

This code snippet is a React component named `CodeDownload` that handles the extraction and download of code blocks from a markdown file. Let's break down the key components of the code:

1. **Dependencies**:
   - `DocumentTextIcon`: This is an icon component imported from the `@heroicons/react` library. It is used to display an icon representing a document.
   - `useLocalization`: This is a custom hook imported from the `LocalizationContext` context. It is used to determine the current language setting (`isSpanish`).

2. **Function `extractCodeBlocks`**:
   - This function takes a `markdownContent` string as input and extracts code blocks from it.
   - It searches for a title using a regex pattern and then locates the "## Component Code" section in the markdown content.
   - Code blocks are identified using triple backticks (```) and are cleaned up by removing the language identifier and replacing remaining backticks with `///`.
   - The function returns an object containing the extracted `title` and `code`.

3. **Function `handleDownloadCodeFiles`**:
   - This function is triggered when the user clicks on the download button.
   - It extracts code blocks from the `selectedFileContent` (markdown content) using `extractCodeBlocks`.
   - If no code blocks are found, an alert is shown.
   - A Blob is created with the extracted code and a download link is generated for the user to download the code as a text file.

4. **Component Rendering**:
   - The component renders two anchor tags (`<a>`) where the first one (`download-code-link`) is hidden and used for downloading the code.
   - The second anchor tag is styled as a button and displays the document icon along with the text "Download Code" or "Descargar Codigo" based on the language setting.
   - Clicking on this button triggers the `handleDownloadCodeFiles` function.

## Example Usage

```jsx
import CodeDownload from './CodeDownload';

const App = () => {
  const markdownContent = `
    title: 'Sample Code'
    
    ## Component Code
    \`\`\`javascript
    const example = () => {
      console.log('Hello, World!');
    }
    \`\`\`
    `;

  return (
    <div>
      <CodeDownload selectedFileContent={markdownContent} />
    </div>
  );
};
```

In this example, the `CodeDownload` component is used within an `App` component with sample `markdownContent`. When the user clicks the "Download Code" button, the code block from the markdown content will be extracted and downloaded as a text file.
  
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
  