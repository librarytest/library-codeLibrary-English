---
  title: 'DirStructureOptions'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # DirStructureOptions.jsx
  # Explanation of Code

The provided code is a React component that displays different directory structure options based on the selected language (English or Spanish). It utilizes the `FolderIcon` component from the `@heroicons/react` library and the `useLocalization` hook from the `LocalizationContext` context.

### Components and Hooks Used:
1. **FolderIcon**: A component from the `@heroicons/react` library that renders a folder icon.
2. **useLocalization**: A custom hook from the `LocalizationContext` context that provides information about the selected language.

### Data Structures:
- Two objects `englishText` and `spanishText` are defined to store text translations for different languages.
- An array `folderStructures` is created to hold different directory structure options along with their corresponding folder names.

### Component Functionality:
- The `DirStructureOptions` component takes two props: `onSelect` (a function to handle selection) and `selectedStructure` (the currently selected structure).
- It checks the selected language (English or Spanish) using the `isSpanish` variable from the `useLocalization` hook.
- It dynamically renders each directory structure option as a clickable element with its name and list of folders.
- When a structure is clicked, it calls the `onSelect` function with the selected structure as an argument.

### Example Usage:
```jsx
import DirStructureOptions from './DirStructureOptions';

const App = () => {
  const handleSelect = (selected) => {
    console.log("Selected Structure:", selected);
  };

  return (
    <div>
      <h1>Select Directory Structure:</h1>
      <DirStructureOptions onSelect={handleSelect} selectedStructure={null} />
    </div>
  );
};
```

In the example usage above, the `DirStructureOptions` component is rendered within an `App` component. When a directory structure option is clicked, the `handleSelect` function is called, logging the selected structure to the console.
  
  ## Component Code
  ```jsx
  import { FolderIcon } from "@heroicons/react/24/outline";
import { useLocalization } from "../../../context/LocalizationContext";

const englishText = {
  defaultStructure: "Default Structure",
  extendedStructure: "Extended Structure",
  reactRecommended: "React Recommended",
  vue: "Vue",
  angular: "Angular",
};

const spanishText = {
  defaultStructure: "Estructura Predeterminada",
  extendedStructure: "Estructura Extendida",
  reactRecommended: "Recomendado por React",
  vue: "Vue",
  angular: "Angular",
};

const DirStructureOptions = ({ onSelect, selectedStructure }) => {
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const folderStructures = [
    {
      name: text.defaultStructure,
      structure: ["introduction", "ui", "sections", "pages", "animations", "helperFunctions"],
    },
    {
      name: text.extendedStructure,
      structure: ["docs", "src", "tests", "config", "scripts", "assets"],
    },
    {
      name: text.reactRecommended,
      structure: ["src", "public", "components", "hooks", "contexts", "utils"],
    },
    {
      name: text.vue,
      structure: ["src", "components", "views", "router", "store", "assets"],
    },
    {
      name: text.angular,
      structure: ["src", "app", "assets", "environments", "styles", "modules"],
    },
  ];

  return (
    <div className="flex overflow-x-scroll gap-4">
      {folderStructures.map((structure, index) => (
        <div
          key={index}
          onClick={() => onSelect(structure)}
          className={`border p-4 rounded-lg cursor-pointer shrink-0 min-w-[250px] ${
            selectedStructure && selectedStructure.name === structure.name ? "border-primary" : "border-transparent"
          }`}
        >
          <h3 className="font-bold mb-2">{structure.name}</h3>
          <ul className="ml-5">
            {structure.structure.map((folder) => (
              <li key={folder} className="flex gap-2">
                <FolderIcon className="w-4 h-4 mr-1" />
                {folder}
              </li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
};

export default DirStructureOptions;
  ```
  