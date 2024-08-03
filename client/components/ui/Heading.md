---
  title: 'Heading'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # Heading.jsx
  # Code Explanation

The provided code is a React component that renders a heading with optional decoration, title, and description. It also includes a fade-in animation transition effect when the component is rendered.

### Components Used:
1. **FadeInTransition**: This component is imported from "../animations/FadeTransition". It is assumed to be a custom component that provides a fade-in animation effect when its children are rendered.

### Functional Component:
- **Heading**: This is a functional component that takes three props - `decoration`, `title`, and `description`. It renders these props along with optional decoration and applies a fade-in transition effect.

### JSX Structure:
1. The `FadeInTransition` component wraps the content of the `Heading` component, applying a fade-in animation effect to its children.
2. Inside the `FadeInTransition`, there are conditional renderings based on the presence of the `decoration` prop.
3. If `decoration` is provided, a span with a class of "badge badge-primary p-4" is rendered with the `decoration` content.
4. Following the decoration, an `h2` element is rendered with the `title` prop as its content.
5. Lastly, a `p` element is rendered with the `description` prop as its content.

### CSS Classes:
- The component uses Tailwind CSS classes like "flex", "gap", "py", "text", "font-bold", "max-w", etc., to style the elements within the component.

### Example Usage:
```jsx
<Heading decoration="New" title="Welcome to our website" description="Explore our services and products." />
```

In this example, the `Heading` component will render with a decoration of "New", a title of "Welcome to our website", and a description of "Explore our services and products." The content will fade in with a transition effect when the component is mounted.
  
  ## Component Code
  ```jsx
  import FadeInTransition from "../animations/FadeTransition";

const Heading = ({ decoration, title, description }) => {
  return (
    <FadeInTransition className="flex flex-col gap-2 sm:gap-4 py-16 sm:py-18">
      {decoration && <span className="badge badge-primary p-4">{decoration}</span>}

      <h2 className="text-3xl sm:text-5xl lg:text-7xl font-bold">{title}</h2>
      <p className="text-base-content/70 max-w-[500px]">{description}</p>
    </FadeInTransition>
  );
};

export default Heading;
  ```
  