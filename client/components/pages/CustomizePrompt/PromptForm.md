---
  title: 'PromptForm'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # PromptForm.jsx
  # Explanation of PromptForm Component

The provided code snippet is a React functional component named `PromptForm`. This component is responsible for rendering a form that allows users to customize a prompt for a specific model. Let's break down the code and understand its functionality:

### Dependencies
- The code imports two components: `ArrowLeftIcon` from the `@heroicons/react` library and an SVG icon `Bot` from a local file `/bot.svg`.

### Component Props
The `PromptForm` component receives the following props:
- `formState`: An object containing the current state of the form.
- `handleInputChange`: A function to handle input changes in the form fields.
- `handleFileChange`: A function to handle file uploads for preview.
- `handleExampleClick`: A function to handle when a prompt example is clicked.
- `promptExamples`: An array of prompt examples to display.
- `handleRunTest`: A function to handle running a test based on the provided instructions.
- `handleSaveInstructions`: A function to handle saving the instructions.
- `navigate`: A function to navigate back.

### Component Structure
1. The component renders a `div` element with a specific styling for layout.
2. Inside the `div`, there is a header section with a title "Customize Prompt" and a back button that triggers the `navigate` function.
3. Following the header, there is a `form` element with various form fields for customization.
4. The form includes:
   - A dropdown select for choosing a model.
   - A file input for uploading a file for preview.
   - A list of prompt examples displayed as buttons.
   - Input fields for instruction name and instructions.
   - Buttons to save instructions and run a test.

### Event Handling
- Event handlers like `handleInputChange`, `handleFileChange`, `handleExampleClick`, `handleSaveInstructions`, and `handleRunTest` are attached to relevant form elements to manage user interactions.

### Usage Example
```jsx
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
```

### Note
- This component serves as a UI form for users to customize prompts for different models by selecting options, uploading files, choosing examples, and providing instructions. The form encapsulates the logic for handling user inputs and interactions within the component itself.
  
  ## Component Code
  ```jsx
  import { ArrowLeftIcon } from '@heroicons/react/24/outline';
import Bot from '/bot.svg';

const PromptForm = ({
  formState,
  handleInputChange,
  handleFileChange,
  handleExampleClick,
  promptExamples,
  handleRunTest,
  handleSaveInstructions,
  navigate
}) => {
  const { selectedModel, instructionName, selectedExample } = formState;

  return (
    <div className="p-4 w-[30%] bg-base-100 border-r-2 border-black shadow-2xl overflow-y-scroll">
      <div className="flex items-center">
        <button
          onClick={() => navigate(-1)}
          className="btn bg-base-100 border-0 shadow-none"
        >
          <ArrowLeftIcon className="w-10" />
        </button>
        <h1 className="text-xl font-bold">Customize Prompt</h1>
      </div>

      <form className="flex flex-col gap-4 mt-4">
        <div className="items-center">
          <img src={Bot} className="w-10 mb-2" alt="Bot" />
          <select
            name="selectedModel"
            className="select w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4 max-w-xs"
            onChange={handleInputChange}
            value={selectedModel}
            required
          >
            <option disabled value="">
              Select Model
            </option>
            <option value="gpt-4-turbo">gpt-4-turbo</option>
            <option value="gpt-3.5-turbo">gpt-3.5-turbo</option>
          </select>
        </div>

        <label className="flex flex-col font-bold">
          File Upload for Preview
          <input
            type="file"
            className="file-input w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4 max-w-xs"
            onChange={handleFileChange}
            required
          />
        </label>

        <label className="flex flex-col font-bold">
          Prompt Examples
          <div className="flex max-w-md gap-2 overflow-x-scroll p-2">
            {promptExamples.map((example, index) => (
              <button
                key={index}
                type="button"
                className="btn btn-outline btn-secondary"
                onClick={() => handleExampleClick(example)}
              >
                {example}
              </button>
            ))}
          </div>
        </label>

        <label className="flex flex-col font-bold">
          Instruction Name
          <input
            type="text"
            name="instructionName"
            className="input w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4 max-w-xs"
            value={instructionName}
            onChange={handleInputChange}
            required
          />
        </label>

        <label className="flex flex-col font-bold">
          Instructions
          <textarea
            name="selectedExample"
            className="textarea w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4 max-w-md h-[150px]"
            placeholder="Instructions"
            value={selectedExample}
            onChange={handleInputChange}
            required
          />
        </label>
        <div className="flex w-full justify-between">
          <button
            type="button"
            className="btn btn-accent"
            onClick={handleSaveInstructions}
          >
            Save Instructions
          </button>
          <button
            type="submit"
            className="btn btn-primary"
            onClick={handleRunTest}
          >
            Run Test
          </button>
        </div>
      </form>
    </div>
  );
};

export default PromptForm;
  ```
  