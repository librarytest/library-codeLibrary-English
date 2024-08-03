---
  title: 'UserPrompts'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # UserPrompts.jsx
  # Code Explanation

This code is a React component that displays a list of user prompts or instructions. It utilizes various React hooks and components to manage state, handle user interactions, and display content.

### External Libraries/Dependencies
- **React**: A JavaScript library for building user interfaces.
- **@heroicons/react**: Provides a set of icons that can be easily used in React components.
- **react-router-dom**: Library for routing in React applications.
- **context/UserInstructions**: Custom context for managing user instructions.
- **context/LocalizationContext**: Custom context for managing localization settings.
- **ui/Breadcrumbs**: Component for displaying breadcrumbs.
- **ui/SideBar**: Component for displaying a sidebar.

### Components and Hooks Used
- **useState**: Hook used to manage local component state.
- **useInstructions**: Custom hook to access and update user instructions.
- **useLocalization**: Custom hook to access localization settings.

### Functions and Components
1. **ListPrompts Component**:
   - **Props**:
     - `id`: Identifier of the prompt.
     - `name`: Name of the prompt.
     - `instructions`: Detailed instructions.
     - `model`: Model associated with the prompt.
     - `onDelete`: Function to handle deletion of the prompt.
   - **Description**:
     - Displays a single prompt with expand/collapse functionality, delete button, and edit button.
     - Toggles the display of detailed instructions when expanded.
     - Provides buttons to delete and edit the prompt.

2. **UserPrompts Component**:
   - **Description**:
     - Retrieves user instructions and localization settings using custom hooks.
     - Handles deletion of a user instruction by making a DELETE request to an API endpoint.
     - Displays a sidebar with information about where instructions are saved and how to modify them.
     - Renders a list of user prompts using the `ListPrompts` component.
     - Handles responsive design for different screen sizes.

### Example Usage
```jsx
import React from "react";
import UserPrompts from "./UserPrompts";

const App = () => {
  return (
    <div>
      <UserPrompts />
    </div>
  );
};

export default App;
```

In this example, the `UserPrompts` component is rendered within the `App` component to display the user prompts interface.
  
  ## Component Code
  ```jsx
  import { useInstructions } from "../context/UserInstructions";
import { useState } from "react";
import {
  PlusCircleIcon,
  MinusCircleIcon,
  TrashIcon,
  PencilIcon,
} from "@heroicons/react/24/outline";
import { Link } from "react-router-dom";
import Breadcrumbs from "../ui/BreadCrumbs";
import SideBar from "../ui/SideBar";
import { useLocalization } from "../context/LocalizationContext";

const englishText = {
  whereSaved: "Where is this saved?",
  modifyingInstructions: "Modifying Instructions",
  whereSavedDesc:
    "Your custom prompts or instructions are saved in a repository named library-username-config inside a JSON file.",
  modifyingInstructionsDesc:
    "While the app can modify the content of the JSON file, deletion of the file needs to be performed directly on GitHub.",
  noInstructions: "No user Default Instructions",
  useOnLaptop: "Oh no, this feature is intended to be used on your Laptop",
};

const spanishText = {
  whereSaved: "¿Dónde se guarda esto?",
  modifyingInstructions: "Modificación de Instrucciones",
  whereSavedDesc:
    "Sus indicaciones o instrucciones personalizadas se guardan en un repositorio llamado library-username-config dentro de un archivo JSON.",
  modifyingInstructionsDesc:
    "Aunque la aplicación puede modificar el contenido del archivo JSON, la eliminación del archivo debe realizarse directamente en GitHub.",
  noInstructions: "No hay instrucciones predeterminadas del usuario",
  useOnLaptop: "Oh no, esta función está destinada a usarse en su portátil",
};

const ListPrompts = ({ id, name, instructions, model, onDelete }) => {
  const [expand, setExpand] = useState(false);

  const handleExpand = () => {
    setExpand(!expand);
  };

  const handleDelete = () => {
    onDelete(id);
  };

  return (
    <div className="flex gap-5 bg-white justify-between mt-4 rounded-2xl w-full px-6 border border-zinc-700 shadow-xl p-10 transition-all">
      <div className="flex flex-col grow shrink-0 basis-0 w-fit transition-all">
        <h3 className="text-lg font-medium leading-7 text-gray-900">{name}</h3>

        <div
          className={`overflow-hidden transition-all duration-500 ${
            expand ? "max-h-96" : "max-h-0"
          }`}
        >
          <p className="mt-2 text-sm leading-6 text-slate-600 max-w-[500px]">
            {instructions}
          </p>
        </div>
        <div className="flex justify-between items-center">
          <span>{model}</span>
          <div className="space-x-4">
            <button className="btn btn-error btn-sm" onClick={handleDelete}>
              <TrashIcon className="w-4" />
            </button>

            <Link
              to={`/customizeprompt?id=${id}`}
              className="btn btn-neutral btn-sm"
            >
              <PencilIcon className="w-4" />
            </Link>
          </div>
        </div>
      </div>

      {expand ? (
        <PlusCircleIcon
          className="self-start w-6 cursor-pointer"
          onClick={handleExpand}
        />
      ) : (
        <MinusCircleIcon
          className="self-start w-6 cursor-pointer"
          onClick={handleExpand}
        />
      )}
    </div>
  );
};

const UserPrompts = () => {
  const { userInstructions, setUserInstructions } = useInstructions();
  const { isSpanish } = useLocalization();
  const text = isSpanish ? spanishText : englishText;

  const handleDeleteInstruction = async (id) => {
    console.log(id);
    try {
      const response = await fetch("/api/delete-instruction", {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ id }),
      });

      if (response.ok) {
        setUserInstructions((prevInstructions) =>
          prevInstructions.filter((instruction) => instruction.id !== id)
        );
      } else {
        console.error("Failed to delete instruction");
      }
    } catch (error) {
      console.error("Failed to delete instruction:", error);
    }
  };

  return (
    <div className="flex bg-orange-100 h-screen">
      <SideBar>
        <div className="w-full space-y-8">
          <div>
            <span className="font-semibold">{text.whereSaved}</span>
            <p className="text-sm">{text.whereSavedDesc}</p>
          </div>

          <div>
            <span className="font-semibold">{text.modifyingInstructions}</span>
            <p className="text-sm">{text.modifyingInstructionsDesc}</p>
          </div>
        </div>
      </SideBar>
      <div className="flex flex-col bg-orange-100 flex-1 h-screen px-20">
        <div className="py-6">
          <Breadcrumbs />
        </div>
        <div className="hidden lg:block h-full overflow-y-scroll">
          {userInstructions.length > 0 ? (
            <div className="h-[90%] overflow-y-scroll ">
              {userInstructions.map((item) => (
                <div key={item.id}>
                  <ListPrompts
                    key={item.id}
                    {...item}
                    onDelete={handleDeleteInstruction}
                  />
                </div>
              ))}
            </div>
          ) : (
            <div className="text-center items-center justify-center">
              <p>{text.noInstructions}</p>
            </div>
          )}
        </div>
        <div className="text-center items-center justify-center lg:hidden h-full">
          <p className="text-xl">{text.useOnLaptop}</p>
        </div>
      </div>
    </div>
  );
};

export default UserPrompts;
  ```
  