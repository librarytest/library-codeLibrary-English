---
  title: 'DirView'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # DirView.jsx
  # Explanation of Code

This code is a React component named `DirView` that displays a directory tree view. It uses hooks like `useState` from React and a custom hook `useLibrary` from the `LibraryContext` context.

### Code Breakdown:

1. **Import Statements:**
   - `useState`: A React hook that allows functional components to have state.
   - `FolderIcon` and `FolderOpenIcon`: Icons imported from the `@heroicons/react` library to represent closed and open folders.
   - `useLibrary`: A custom hook imported from the `LibraryContext` context.

2. **Functional Component `DirView`:**
   - Initializes state variables `openFolders` and `cache` using `useState`.
   - Defines a function `toggleFolder` to toggle the visibility of a folder and fetch its contents if not cached.
   - Defines a function `handleDirectoryClick` to handle click events on directories, toggling folders and setting the upload path.
   - Defines a recursive function `renderTree` to render the directory tree structure recursively.

3. **Usage:**
   - The component fetches contents from the library context using `useLibrary`.
   - Clicking on a directory triggers the `handleDirectoryClick` function, which toggles the folder open/close state and sets the upload path.
   - The `renderTree` function recursively renders the directory structure based on the contents fetched.

### Example Usage:
```jsx
// Usage of DirView component
<DirView />
```

### Dependencies:
- React: The core library for building the user interface.
- @heroicons/react: Provides a set of icons for use in the application.
- LibraryContext: Custom context providing library-related functionality.

This code efficiently manages the state of open folders, caches fetched contents, and renders a dynamic directory tree view in a React application.
  
  ## Component Code
  ```jsx
  import { useState } from 'react';
import { FolderIcon, FolderOpenIcon } from '@heroicons/react/24/outline';
import { useLibrary } from '../../context/LibraryContext';

const DirView = () => {
  const { contents, fetchContents, setUploadPath } = useLibrary();
  const [openFolders, setOpenFolders] = useState({});
  const [cache, setCache] = useState({});

  const toggleFolder = async (path) => {
    if (!openFolders[path] && !cache[path]) {
      const fetchedContents = await fetchContents(path);
      setCache((prevCache) => ({
        ...prevCache,
        [path]: fetchedContents,
      }));
    }
    setOpenFolders((prev) => ({
      ...prev,
      [path]: !prev[path],
    }));
  };

  const handleDirectoryClick = (path) => {
    toggleFolder(path);
    setUploadPath(path);
  };

  const renderTree = (items, parentPath = '') => (
    <ul className="rounded-lg w-full">
      {items.map((item) => (
        <li key={item.path}>
          {item.type === 'dir' ? (
            <div>
              <div
                onClick={() => handleDirectoryClick(`${parentPath}/${item.name}`)}
                className="flex items-center cursor-pointer"
              >
                {openFolders[`${parentPath}/${item.name}`] ? (
                  <FolderOpenIcon className="w-4 h-4 mr-1" />
                ) : (
                  <FolderIcon className="w-4 h-4 mr-1" />
                )}
                <span>{item.name}</span>
              </div>
              {openFolders[`${parentPath}/${item.name}`] && (
                <div className="ml-4">
                  {renderTree(cache[`${parentPath}/${item.name}`] || [], `${parentPath}/${item.name}`)}
                </div>
              )}
            </div>
          ) : null}
        </li>
      ))}
    </ul>
  );

  return <div>{renderTree(contents)}</div>;
};

export default DirView;
  ```
  