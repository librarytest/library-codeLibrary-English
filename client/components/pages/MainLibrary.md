---
  title: 'MainLibrary'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # MainLibrary.jsx
  # Explanation of MainLibrary Component

The provided code is a React functional component named `MainLibrary`. It is a part of a larger application and is responsible for rendering a main library page where users can create new libraries, view existing libraries, and perform other related actions.

### External Dependencies
- **Heroicons**: The code imports the `PlusCircleIcon` component from the `@heroicons/react` library to display a plus icon for creating a new library.
- **React Router DOM**: The `useNavigate` hook is imported from `react-router-dom` to enable navigation to a specific library page after successful creation.
- **Contexts and UI Components**: Various custom UI components like `Heading`, `BentoCard`, `Skeleton`, `FadeInTransition`, `Popup`, and `SideBar` are imported from their respective paths to structure the main library page.

### Functions and Hooks
- **useMainLibrary**: The `useMainLibrary` hook is used to access data related to repositories, loading states, modal states, and functions like `onSubmit` for form submission.
- **useLocalization**: The `useLocalization` hook is used to access localized text data for the main library page.

### Functions in MainLibrary Component
1. **handleClose**: This function is called to close the modal by setting `isModalOpen` state to `false`.
2. **handleSuccess**: This function is called upon successful library creation. It closes the modal and navigates the user to the newly created library page.
3. **onSubmit**: This function is used in the form submission to create a new library.

### Component Structure
1. The component is divided into two main sections using flex layout: a sidebar and the main content area.
2. The sidebar displays information about creating and modifying repositories.
3. The main content area includes a heading, a button to create a new library, a modal for creating a library, and a list of existing libraries displayed using `BentoCard` components with a fade-in animation.

### Example Usage
```jsx
import React from 'react';
import MainLibrary from './MainLibrary';

const App = () => {
  return (
    <div>
      <MainLibrary />
    </div>
  );
};

export default App;
```

In this example, the `MainLibrary` component is rendered within an `App` component to display the main library page in a React application.
  
  ## Component Code
  ```jsx
  import { PlusCircleIcon } from "@heroicons/react/24/outline";
import Heading from "../ui/Heading";
import BentoCard from "../ui/BentoCard";
import Skeleton from "../ui/Skeleton";
import FadeInTransition from "../animations/FadeTransition";
import Popup from "../ui/PopUp";
import { useMainLibrary } from "../context/MainLibraryContext";
import SideBar from "../ui/SideBar";
import { useNavigate } from "react-router-dom";
import { useLocalization } from "../context/LocalizationContext";
const MainLibrary = () => {
  const {
    repositories,
    loading,
    isLoading,
    isSuccess,
    isError,
    isModalOpen,
    setIsModalOpen,
    newRepoName,
    setNewRepoName,
    newRepoDescription,
    setNewRepoDescription,
    onSubmit,
  } = useMainLibrary();
  const { mainLibraryPageData } = useLocalization();

  const navigate = useNavigate();

  const handleClose = () => {
    setIsModalOpen(false);
  };

  const handleSuccess = () => {
    setIsModalOpen(false);
    navigate(`/library/library-${newRepoName}`);
  };

  const { sidebar, heading, popup, form } = mainLibraryPageData;
  return (
    <div className="flex bg-orange-100 h-screen">
      <SideBar>
        <div className="w-full space-y-8">
          <div>
            <span className="font-semibold">
              {sidebar.creatingRepositoryTitle}
            </span>
            <p className="text-sm">{sidebar.creatingRepositoryDescription}</p>
          </div>
          <div>
            <span className="font-semibold">{sidebar.deleteModifyTitle}</span>
            <p className="text-sm">{sidebar.deleteModifyDescription}</p>
          </div>
        </div>
      </SideBar>
      <div className="p-10 w-full h-full overflow-y-scroll">
        <Heading title={heading.title} decoration={heading.decoration} />

        <div className="flex flex-wrap justify-evenly gap-10 mt-10">
          <button
            className="btn hidden lg:flex flex-col justify-center w-[250px] h-[290px] rounded-2xl btn-outline btn-primary "
            onClick={() => setIsModalOpen(true)}
          >
            <PlusCircleIcon className="w-10" />
            <span>{popup.createLibrary}</span>
          </button>

          <Popup
            popupId="create_library_modal"
            isOpen={isModalOpen}
            onClose={handleClose}
            title={popup.title}
            description={popup.description}
            isLoading={isLoading}
            isSuccess={isSuccess}
            isError={isError}
            successMessage={popup.successMessage}
            errorMessage={popup.errorMessage}
            customStyle="w-[500px] p-5 rounded-2xl"
            onSuccess={handleSuccess} // Use the handleSuccess function
          >
            <form onSubmit={onSubmit} className="space-y-4">
              <input
                type="text"
                value={newRepoName}
                onChange={(e) => setNewRepoName(e.target.value)}
                placeholder={form.libraryNamePlaceholder}
                required
                className="input w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4"
              />
              <textarea
                value={newRepoDescription}
                onChange={(e) => setNewRepoDescription(e.target.value)}
                placeholder={form.libraryDescriptionPlaceholder}
                required
                className="textarea w-full border-b-4 border-black shadow-2xl focus:ring-0 focus:border-black focus:border-b-4"
              />
              <button
                type="submit"
                disabled={isLoading}
                className="btn btn-primary"
              >
                 {form.createLibraryButton}
              </button>
            </form>
          </Popup>

          {loading ? (
            <Skeleton />
          ) : (
            repositories.map((repo) => (
              <FadeInTransition key={repo.id} delay={0.2}>
                <BentoCard
                  title={repo.name}
                  description={repo.description || "No description provided"}
                />
              </FadeInTransition>
            ))
          )}
        </div>
      </div>
    </div>
  );
};

export default MainLibrary;
  ```
  