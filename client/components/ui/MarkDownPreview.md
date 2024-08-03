---
  title: 'MarkDownPreview'
  description: 'component description'
  pubDate: 'August 3, 2024'
  author: 'librarytest'
  ---
  
  
  
  # MarkDownPreview.jsx
  # Explanation of MarkDownPreview Component

The `MarkDownPreview` component is a React component that renders a markdown preview of a selected file content with syntax highlighting for code blocks. It utilizes the `react-markdown` library for rendering markdown content and `react-syntax-highlighter` for syntax highlighting.

### Functions:
1. **removeFrontMatter**:
   - This function takes the selected file content as input and removes the front matter from it. The front matter is typically metadata at the beginning of a markdown file enclosed within `---`.
   
### Dependencies:
1. **react-markdown**:
   - Library for rendering markdown content in React applications.
2. **rehype-raw**:
   - Plugin for `react-markdown` that allows raw HTML in markdown.
3. **rehype-sanitize**:
   - Plugin for `react-markdown` that sanitizes HTML in markdown to prevent XSS attacks.
4. **react-syntax-highlighter**:
   - Library for syntax highlighting code blocks in React applications.
5. **react-syntax-highlighter/dist/esm/styles/prism**:
   - Style for syntax highlighting provided by `react-syntax-highlighter`.
6. **github-markdown-css**:
   - CSS file for styling the markdown content to resemble GitHub markdown styling.

### Usage:
```jsx
import MarkDownPreview from './MarkDownPreview';

const App = () => {
    const selectedFileContent = `---
title: Sample Markdown
description: This is a sample markdown file
pubDate: 2022-01-01
author: John Doe
---

# Sample Markdown Content

\`\`\`javascript
const greeting = "Hello, World!";
console.log(greeting);
\`\`\`
`;

    return (
        <div>
            <MarkDownPreview selectedFileContent={selectedFileContent} customStyle='px-10 pt-10 pb-32' />
        </div>
    );
};
```

In the example above, the `MarkDownPreview` component is used to render a markdown preview of the `selectedFileContent` with custom styling applied. The component removes the front matter from the content and provides syntax highlighting for code blocks using `react-syntax-highlighter`. The resulting markdown preview is displayed with GitHub markdown styling.
  
  ## Component Code
  ```jsx
  import Markdown from 'react-markdown';
import rehypeRaw from 'rehype-raw';
import rehypeSanitize from 'rehype-sanitize';
import { Prism as SyntaxHighlighter } from 'react-syntax-highlighter';
import { materialDark } from 'react-syntax-highlighter/dist/esm/styles/prism';
import 'github-markdown-css/github-markdown.css';

const MarkDownPreview = ({ selectedFileContent, customStyle='px-10 pt-10 pb-32' }) => {
    // Function to remove the front matter
    const removeFrontMatter = (content) => {
        return content.replace(/---\n[\s\S]*?title:.*\n[\s\S]*?description:.*\n[\s\S]*?pubDate:.*\n[\s\S]*?author:.*\n[\s\S]*?---\n/, '');
    };

    // Clean the selected file content
    const cleanedContent = removeFrontMatter(selectedFileContent);

    const MarkdownComponents = {
        code({ inline, className, children, ...props }) {
            const match = /language-(\w+)/.exec(className || "");
            return !inline && match ? (
                <SyntaxHighlighter
                    style={materialDark}
                    PreTag="div"
                    language={match[1]}
                    // eslint-disable-next-line react/no-children-prop
                    children={String(children).replace(/\n$/, "")}
                    {...props}
                />
            ) : (
                <code className={className ? className : ""} {...props}>
                    {children}
                </code>
            );
        },
    };

    return (
        <div className={`markdown-body w-full h-full bg-white border border-2 border-black overflow-y-scroll  ${customStyle}`}>
            <Markdown
                rehypePlugins={[rehypeRaw, rehypeSanitize]}
                components={MarkdownComponents}
            >
                {cleanedContent}
            </Markdown>
            
        </div>
    );
};

export default MarkDownPreview;
  ```
  