---
  title: 'Hero'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # Hero.jsx
  # Explanation of Hero Component Code

The provided code is a React functional component named `Hero` that takes in props such as `reverse`, `title`, `description`, and `image`. The component structure includes a fade-in animation effect using the `FadeInSpring` component imported from "../animations/FadeInSpring".

## Components and Libraries Used
- **React**: The code is written in React, a JavaScript library for building user interfaces.
- **FadeInSpring**: An external component for adding a fade-in animation effect.

## Code Explanation
1. **Import Statement**:
   - `import FadeInSpring from "../animations/FadeInSpring";`: Imports the `FadeInSpring` component from the "../animations/FadeInSpring" file.

2. **Hero Component**:
   - The `Hero` component is a functional component that takes props `reverse`, `title`, `description`, and `image`.
   - It returns JSX elements wrapped inside the `FadeInSpring` component for a fade-in animation effect.

3. **JSX Structure**:
   - The component renders a `div` element with the class `flex flex-col w-full items-center justify-center gap-10 max-w-5xl mx-auto` which contains two child elements.
   - The first child is a `div` element with a fixed width and height, displaying an image passed through the `image` prop.
   - The second child is a `div` element containing the title and description passed through the `title` and `description` props, respectively.

4. **Conditional Styling**:
   - The `className` of the main `div` element is conditionally set based on the `reverse` prop. If `reverse` is true, the class "sm:flex-row-reverse" is applied; otherwise, "sm:flex-row" is applied.

5. **Styling**:
   - Various CSS classes are applied to style the elements, such as setting widths, heights, borders, and text styles.

## Example Usage
```jsx
<Hero
  reverse={false}
  title="Welcome to Our Website"
  description="Explore our services and products."
  image="path/to/image.jpg"
/>
```

In this example, the `Hero` component is used with the specified props. The `reverse` prop is set to `false`, a title and description are provided, and an image path is passed for display.
  
  ## Component Code
  ```jsx
  import FadeInSpring from "../animations/FadeInSpring";
export default function Hero({ reverse = false, title, description, image }) {
  return (
    <FadeInSpring>
    <div
      className={`flex flex-col  w-full items-center justify-center gap-10 max-w-5xl mx-auto  ${
        reverse ? "sm:flex-row-reverse" : "sm:flex-row"
      }`}
    >
      <div className="w-[350px] h-[350px]  relative  overflow-hidden border border-white/10 rounded-2xl box-shadow relative">
        <img src={image} alt={image} className="w-full" />
      </div>
      <div className="grid gap-4 sm:w-[30%] text-start">
        <h2 className="text-4xl font-bold tracking-tighter">{title}</h2>
        <p className="text-base-content/70">{description}</p>
      </div>
    </div>
    </FadeInSpring>
  );
}
  ```
  