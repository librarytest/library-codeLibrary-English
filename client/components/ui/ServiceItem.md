---
  title: 'ServiceItem'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # ServiceItem.jsx
  ## Explanation of ServiceItem Component

The provided code snippet is a React functional component named `ServiceItem`. This component takes in four props: `image`, `title`, `description`, and `className`.

### Purpose
The purpose of this component is to render a service item with an image, title, and description in a styled container.

### Props
- `image`: Represents the URL of the image to be displayed.
- `title`: Represents the title of the service item.
- `description`: Represents the description of the service item.
- `className`: Represents any additional CSS classes that can be passed to customize the styling of the component.

### Component Structure
1. The component returns a `div` element with the following classes:
   - `flex flex-col`: Displays the children elements in a column layout.
   - `w-[300px] h-[350px]`: Sets the width and height of the container to 300px and 350px respectively.
   - `justify-center items-center`: Aligns the content both horizontally and vertically in the center.
   - `shrink-0`: Prevents the container from shrinking.
   - `gap-2`: Adds a gap of 2 units between the child elements.
   - `border-2 border-black`: Adds a 2px black border around the container.
   - `p-4`: Adds padding of 4 units inside the container.
   - `${className}`: Allows for additional custom classes to be passed to the component.

2. Inside the `div`, there are three child elements:
   - An `img` element displaying the service item image with the specified `src`, `className`, and `alt` attributes.
   - An `h3` element displaying the title of the service item with a font size of 2xl and bold styling.
   - A `p` element displaying the description of the service item with reduced text size and opacity.

### Example Usage
```jsx
import ServiceItem from './ServiceItem';

const App = () => {
  return (
    <ServiceItem
      image="service.jpg"
      title="Web Development"
      description="Professional web development services tailored to your needs."
      className="custom-class"
    />
  );
};
```

In the example above, the `ServiceItem` component is imported and used within a parent component (`App`). The component is passed the necessary props such as `image`, `title`, `description`, and `className` to render a service item with custom content.
  
  ## Component Code
  ```jsx
  const ServiceItem = ({
  image,
  title,
  description,
  className,
}) => {
  return (
    <div
      className={`flex flex-col w-[300px] h-[350px] justify-center items-center shrink-0 gap-2 border-2 border-black p-4 ${className}`}
    >
      <img
        src={image}
        className="w-6 shadow-sm aspect-square bg-primary"
        alt={title}
      />
      <h3 className="text-2xl font-bold">{title}</h3>
      <p className="text-sm text-base-content/70">{description}</p>
    </div>
  );
};

export default ServiceItem;
  ```
  