---
  title: 'MainLibraryContext'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # MainLibraryContext.jsx
  ## Explanation of MainLibraryProvider Component

The provided code is a React component that serves as a context provider for a main library feature in a web application. Here is a breakdown of the code:

1. **Imports**:
   - The code imports necessary functions and hooks from React and a custom hook `useLoadingIndicator` from '../hooks/useLoadingIndicator'.
   
2. **MainLibraryContext**:
   - A context named `MainLibraryContext` is created using `createContext()` from React. This context will be used to pass data down the component tree.

3. **useMainLibrary Hook**:
   - `useMainLibrary` is a custom hook created using `useContext` with `MainLibraryContext`. It allows components to access the data provided by the `MainLibraryProvider`.

4. **MainLibraryProvider Component**:
   - This component takes a `children` prop and wraps its content with the `MainLibraryContext.Provider`.
   - It initializes various states using `useState` for repositories, loading status, new repository name and description, and user profile.
   - Two `useEffect` hooks are used to fetch user profile and repositories when the component mounts.
   - `handleCreateRepository` function sends a POST request to create a new repository and updates the state with the new repository data.
   - `onSubmit` function is triggered on form submission to handle loading state and repository creation.
   - The component provides the following values through the context:
     - repositories: Array of repositories
     - loading: Loading status
     - isLoading: Loading indicator status
     - isSuccess: Success indicator status
     - isError: Error indicator status
     - isModalOpen: Modal open indicator
     - setIsModalOpen: Function to set modal open status
     - newRepoName: Name of the new repository
     - setNewRepoName: Function to set the new repository name
     - newRepoDescription: Description of the new repository
     - setNewRepoDescription: Function to set the new repository description
     - onSubmit: Function to handle form submission
     - userProfile: User profile data

5. **External Dependencies**:
   - The code relies on the `useLoadingIndicator` custom hook for managing loading indicators.

### Example Usage:
```jsx
import React from 'react';
import { MainLibraryProvider, useMainLibrary } from './MainLibraryProvider';

const App = () => {
  return (
    <MainLibraryProvider>
      <MainLibraryContent />
    </MainLibraryProvider>
  );
};

const MainLibraryContent = () => {
  const { repositories, loading, newRepoName, setNewRepoName, onSubmit } = useMainLibrary();

  return (
    <div>
      {loading ? <p>Loading...</p> : (
        <form onSubmit={onSubmit}>
          <input type="text" value={newRepoName} onChange={(e) => setNewRepoName(e.target.value)} />
          <button type="submit">Create Repository</button>
        </form>
      )}
      <ul>
        {repositories.map(repo => (
          <li key={repo.id}>{repo.name}</li>
        ))}
      </ul>
    </div>
  );
};

export default App;
```

In this example, the `MainLibraryProvider` wraps the `MainLibraryContent` component, which uses the `useMainLibrary` hook to access repository data, loading status, and functions to create a new repository.
  
  ## Component Code
  ```jsx
  /* eslint-disable react/prop-types */
import { createContext, useContext, useState, useEffect } from 'react';
import useLoadingIndicator from '../hooks/useLoadingIndicator';

const MainLibraryContext = createContext();

export const useMainLibrary = () => useContext(MainLibraryContext);

export const MainLibraryProvider = ({ children }) => {
  const { isLoading, isSuccess, isError, handleLoading, isModalOpen, setIsModalOpen } = useLoadingIndicator();
  const [repositories, setRepositories] = useState([]);
  const [loading, setLoading] = useState(true);
  const [newRepoName, setNewRepoName] = useState("");
  const [newRepoDescription, setNewRepoDescription] = useState("");
  const [userProfile, setUserProfile] = useState(null); // New state for user profile

  useEffect(() => {
    const fetchUserProfile = async () => {
      try {
        const response = await fetch("/api/user/profile", {
          method: "GET",
          headers: { "Content-Type": "application/json" },
          credentials: "include",
        });

        if (response.ok) {
          const data = await response.json();
          setUserProfile(data.profile);
        } else {
          console.error("Failed to fetch user profile");
        }
      } catch (error) {
        console.error("Error:", error);
      }
    };

    const fetchRepositories = async () => {
      try {
        const response = await fetch("/api/repositories/library", {
          method: "GET",
          headers: { "Content-Type": "application/json" },
          credentials: "include",
        });

        if (response.ok) {
          const data = await response.json();
          setRepositories(data.repositories);
        } else {
          console.error("Failed to fetch repositories");
        }
      } catch (error) {
        console.error("Error:", error);
      } finally {
        setLoading(false);
      }
    };

    fetchUserProfile();
    fetchRepositories();
  }, []);

  const handleCreateRepository = async () => {
    const response = await fetch("/api/create-repository", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      credentials: "include",
      body: JSON.stringify({ name: newRepoName, description: newRepoDescription }),
    });

    if (response.ok) {
      const data = await response.json();
      setRepositories((prevRepos) => [...prevRepos, data.repository]);
      
     
    
    } else {
      console.error("Failed to create repository");
      throw new Error("Failed to create repository");
    }
  };

  const onSubmit = (e) => {
    e.preventDefault();
    handleLoading(handleCreateRepository);
  };

  return (
    <MainLibraryContext.Provider
      value={{
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
        userProfile, // Provide user profile in the context
      }}
    >
      {children}
    </MainLibraryContext.Provider>
  );
};
  ```
  