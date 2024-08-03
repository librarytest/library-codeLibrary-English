---
  title: 'TransformCode'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # TransformCode.jsx
  # Code Explanation

This code is a React component named `TransformCode` that allows users to transform code into different formats or optimize it based on selected instructions. The component utilizes state management with `useState` hook from React to handle loading state, error state, and transformed code state. It also uses a custom hook `useLocalization` from the `LocalizationContext` to determine the language for the text displayed.

## Functions and Constructs

### `extractCodeBlocks`
- **Description**: This function extracts code blocks from the markdown content provided.
- **Operation**: It searches for a specific title and code blocks within the markdown content and returns the extracted code blocks.
- **Usage**: Used to extract code blocks from the markdown content for transformation.

### `handleTransform`
- **Description**: This function handles the transformation of code based on the selected instructions.
- **Operation**: It sends a POST request to a specified endpoint with the code content and transformation instructions, then updates the transformed code state.
- **Usage**: Triggered when a transformation button is clicked to transform the code.

## External Dependencies
- **React**: A JavaScript library for building user interfaces.
- **MarkDownPreview**: A custom component for rendering Markdown content.
- **Modal**: A custom component for displaying modals.
- **LocalizationContext**: A context for managing localization settings.

## Example Usage
```jsx
import TransformCode from 'path/to/TransformCode';

const App = () => {
  const selectedFileContent = `# Sample Markdown Content`;

  return (
    <div>
      <TransformCode selectedFileContent={selectedFileContent} />
    </div>
  );
};
```

In this example, the `TransformCode` component is imported and rendered within an `App` component, passing a sample markdown content as `selectedFileContent`. The user can then interact with the component to transform the code.
  
  ## Component Code
  ```jsx
  import { useState } from "react";
import MarkDownPreview from "../../../ui/MarkDownPreview";
import Modal from "../../../ui/Modal";
import { useLocalization } from "../../../context/LocalizationContext";

const englishText = {
  buttonTitle: "Transform Code",
  modalTitle: "Code Transformation",
  modalDescription: "This feature allows you to transform code into different formats or optimize it based on your selected instructions. Please note that this is an experimental feature. Ensure to check and revise the transformed code before using it in production.",
  originalCode: "Original Code",
  optimizeCode: "Optimize Code",
  reactNative: "React Native",
  typeScriptVersion: "TypeScript Version",
  nonTypeScriptVersion: "Non TypeScript Version",
  angular: "Angular",
  vue: "Vue",
  failedToTransform: "Failed to transform code",
  transformedCode: "Transformed Code"
};

const spanishText = {
  buttonTitle: "Convertir Codigo",
  modalTitle: "Transformación de Código",
  modalDescription: "Esta función te permite transformar el código en diferentes formatos u optimizarlo según las instrucciones seleccionadas. Tenga en cuenta que esta es una función experimental. Asegúrese de revisar y corregir el código transformado antes de usarlo en producción.",
  originalCode: "Codigo Original",
  optimizeCode: "Optimizar Codigo",
  reactNative: "React Native",
  typeScriptVersion: "Versión TypeScript",
  nonTypeScriptVersion: "Versión No TypeScript",
  angular: "Angular",
  vue: "Vue",
  failedToTransform: "Error al transformar el código",
  transformedCode: "Codigo Transformado"
};

const TransformCode = ({ selectedFileContent }) => {
  const [transformedCode, setTransformedCode] = useState("");
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const extractCodeBlocks = (markdownContent) => {
    const titleMatch = markdownContent.match(/title:\s*'([^']+)'/);
    const title = titleMatch ? titleMatch[1] : "code";

    const componentCodeStart = markdownContent.indexOf("## Component Code");
    if (componentCodeStart === -1) return { title, code: "" };

    const codeSection = markdownContent.slice(componentCodeStart);
    const codeBlockRegex = /```[\s\S]*?```/g;
    const codeBlocks = codeSection.match(codeBlockRegex) || [];

    const cleanedCodeBlocks = codeBlocks
      .map((block) => {
        // Remove the ``` and language identifier
        const cleanedBlock = block.trim();
        // Replace any remaining ``` with ///
        return cleanedBlock;
      })
      .join("\n\n");

    return {
      title,
      code: cleanedCodeBlocks,
    };
  };

  const handleTransform = async (transformInstructions) => {
    const { code } = extractCodeBlocks(selectedFileContent);

    if (!code) {
      alert("No code found to transform");
      return;
    }

    setIsLoading(true);
    setIsError(false);

    try {
      const response = await fetch("/ai/transform-code", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ content: code, transformInstructions }),
      });

      if (!response.ok) {
        throw new Error("Failed to transform code");
      }

      const result = await response.json();
      console.log(result);
      setTransformedCode(result); // Adjust based on your API response structure
    } catch (error) {
      console.error("Error transforming code:", error);
      setIsError(true);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <>
      <Modal
        buttonTitle={text.buttonTitle}
        modalId={"transformCode"}
        customStyle="w-[95%]  h-[95%]  overflow-y-scroll px-10 pb-20"
      >
        <div className="w-full flex flex-col items-center justify-center text-center mb-4">
          <h2 className="text-5xl font-bold">{text.modalTitle}</h2>
          <p className="text-xs max-w-xl">{text.modalDescription}</p>
        </div>
        <div className="w-full flex gap-4 h-full overflow-y-scroll">
          <div className="w-[40%]">
            <h2 className="font-semibold mb-4">{text.originalCode}</h2>
            <MarkDownPreview
              selectedFileContent={extractCodeBlocks(selectedFileContent).code}
              customStyle={"bg-red-200"}
            />
          </div>
          <div className="flex flex-col self-center gap-4">
            <button
              className="btn btn-primary"
              onClick={() => handleTransform("Optimize the code")}
              disabled={isLoading}
            >
              {isLoading ? "Optimizing..." : text.optimizeCode}
            </button>
            <button
              className="btn btn-secondary"
              onClick={() => handleTransform("Convert to React Native")}
              disabled={isLoading}
            >
              {isLoading ? "Converting..." : text.reactNative}
            </button>
            <button
              className="btn btn-accent"
              onClick={() => handleTransform("Convert to TypeScript")}
              disabled={isLoading}
            >
              {isLoading ? "Converting..." : text.typeScriptVersion}
            </button>
            <button
              className="btn btn-neutral"
              onClick={() => handleTransform("Convert to JavaScript")}
              disabled={isLoading}
            >
              {isLoading ? "Converting..." : text.nonTypeScriptVersion}
            </button>
            <button
              className="btn btn-error"
              onClick={() => handleTransform("Convert to Angular")}
              disabled={isLoading}
            >
              {isLoading ? "Converting..." : text.angular}
            </button>
            <button
              className="btn btn-success"
              onClick={() => handleTransform("Convert to Vue")}
              disabled={isLoading}
            >
              {isLoading ? "Converting..." : text.vue}
            </button>
          </div>
          <div className="w-[40%]">
            {isError && <p className="text-red-500">{text.failedToTransform}</p>}
            {transformedCode && (
              <>
                <h2 className="font-semibold mb-4">{text.transformedCode}</h2>
                <MarkDownPreview selectedFileContent={transformedCode} customStyle={"bg-green-200"} />
              </>
            )}
          </div>
          <div className="h-32"></div>
        </div>
      </Modal>
    </>
  );
};

export default TransformCode;
  ```
  