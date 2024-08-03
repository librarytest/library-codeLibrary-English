---
  title: 'NavigationButton'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # NavigationButton.jsx
  # Explanation of Code

The provided code is a React component that generates a navigation menu with buttons for different actions. It uses the React Router for navigation and Heroicons for icons. The component also interacts with a localization context to display text in either English or Spanish based on the user's language preference.

## Components and Libraries Used
- **React Router**: Used for navigation within the React application.
- **Heroicons**: Provides a set of icons for use in the application.
- **LocalizationContext**: A custom context that handles localization settings.

## Code Explanation
1. **Import Statements**:
   - Imports the necessary components and libraries like `Link` from React Router, icons from Heroicons, and the `useLocalization` hook from the custom LocalizationContext.

2. **Text Constants**:
   - Defines text constants for English and Spanish languages. These constants contain translations for different navigation elements like custom prompts, create prompt, and sign out.

3. **NavigationButton Component**:
   - A functional component that renders a navigation button with an icon and title.
   - Accepts props like `to` for the destination path, `icon` for the icon component, and `title` for the button text.
   - Uses the `Link` component from React Router to create a clickable button with the specified icon and title.

4. **PromptNavigations Component**:
   - The main component that renders the navigation menu.
   - Uses the `useLocalization` hook to determine the user's language preference.
   - Displays different text based on the language setting.
   - Defines a function `handleSignOut` that sends a sign-out request to the server and redirects to the home page upon successful sign-out.
   - Renders navigation buttons for user prompts, customizing prompts, and a sign-out button.
   - The sign-out button triggers the `handleSignOut` function when clicked.

## Example Usage
```jsx
<PromptNavigations />
```

This code snippet would render the `PromptNavigations` component, displaying a navigation menu with buttons for custom prompts, creating prompts, and signing out. The text displayed on the buttons will be in English or Spanish based on the user's language preference.
  
  ## Component Code
  ```jsx
  import { Link } from "react-router-dom";
import {
  AdjustmentsHorizontalIcon,
  Cog6ToothIcon,
} from "@heroicons/react/24/outline";
import { useLocalization } from "../context/LocalizationContext";

const textEn = {
  customPrompts: "Custom Prompts",
  createPrompt: "Create Prompt",
  signOut: "Sign Out",
};

const textEs = {
  customPrompts: "Prompts Personalizados",
  createPrompt: "Crear Prompt",
  signOut: "Cerrar Sesión",
};

function NavigationButton({ to, icon: Icon, title }) {
  return (
    <Link
      to={to}
      className={`rounded-2xl hidden lg:flex items-center gap-2  relative z-30`}
    >
      {Icon && <Icon className="w-6 " />}
      <p className="text-sm">{title}</p>
    </Link>
  );
}

export default function PromptNavigations() {
  const { isSpanish } = useLocalization();
  const text = isSpanish ? textEs : textEn;

  const handleSignOut = async () => {
    try {
      const response = await fetch('/signout', {
        method: 'GET',
        credentials: 'include', // To include cookies
      });

      if (response.ok) {
        window.location.href = '/'; // Fallback in case server does not handle redirect
      } else {
        console.error('Failed to sign out');
      }
    } catch (error) {
      console.error('An error occurred during sign out', error);
    }
  };

  return (
    <div className="flex flex-col gap-4 mt-auto">
      <NavigationButton
        to="/userPrompts"
        icon={AdjustmentsHorizontalIcon}
        title={text.customPrompts}
      />
      <NavigationButton
        to="/customizeprompt"
        icon={Cog6ToothIcon}
        title={text.createPrompt}
      />
      <button className="btn btn-secondary btn-sm" onClick={handleSignOut}>
        {text.signOut}
      </button>
    </div>
  );
}
  ```
  